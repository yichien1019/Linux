# ð HAPROXY
## ð åç½®ä½æ¥­
### ð 7-1ä¸»æ© (192.168.56.5)
```
01 # cd /var/www/html
02 # ls
    files  hi.php  index.html  test.php
03 # gedit index.html 
    centos 7-1
04 # systemctl stop firewalld
```
### ð 7-2ä¸»æ© (192.168.56.103)
```
05 # cd /var/www/html
06 # ls
    index.html
07 # gedit index.html 
    centos 7-2
08 # systemctl stop firewalld
```
### ð 7-3ä¸»æ© (192.168.56.106)
```
09 # cd /var/www/html
10 # ls 
    files  index.html
11 # gedit index.html 
    centos 7-3
12 # systemctl stop firewalld
```
## ð å®è£ååä»½ (7-1 ä¸»æ©)
```
13 # yum install epel-release
14 # yum install haproxy -y
15 # systemctl start httpd
16 # cp /etc/haproxy/haproxy.cfg  /etc/haproxy/haproxy.cfg.bak2
17 # gedit /etc/haproxy/haproxy.cfg
    global
        daemon
        chroot /var/lib/haproxy
        user haproxy
        group haproxy
        stats timeout 30s

    defaults
        mode http
        log global
        option httplog
        option dontlognull
        timeout connect 5000
        timeout client 50000
        timeout server 50000

    frontend http_front
        bind *:80
    \
        stats uri /haproxy?stats
        default_backend http_back

    backend http_back
        balance roundrobin
        server server_name1 192.168.56.103:80 check   ï¼7-2 IPï¼
        server server_name2 192.168.56.106:80 check   ï¼7-3 IPï¼
```
* :80æè¢«httpdå ç¨æä»¥httpdè¦åéé
## ð éå Haproxy
```
18 # netstat -tunlp | grep 80
    tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      6235/haproxy        
    tcp6       0      0 :::80                   :::*                    LISTEN      6209/httpd
19 # systemctl stop httpd
20 # systemctl disable httpd
21 # systemctl status httpd
22 # systemctl start haproxy
23 # systemctl status haproxy
    â haproxy.service - HAProxy Load Balancer
    Loaded: loaded (/usr/lib/systemd/system/haproxy.service; enabled; vendor preset: disabled)
    Active: active (running) since Sat 2021-12-25 16:55:42 CST; 3s ago
```
## ð è£åè³æ
* [åæéç½® HAProxy Configuration](https://ci-jie.github.io/2020/10/25/HAProxy-Data-Plane-API/)



ðï¸ editor : yi-chien Liu