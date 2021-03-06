# ð Linuxç³»çµ±èªååéç¶­ç¬¬åä¸é±ç­è¨20220427
# ð PROMETHEUS
## ð è¨­å®
![](pic/prometheus_introduce.jpg)
### ð ç¬¬ä¸æ­¥ : ä¿®æ¹èæ¬æ©åç¨±
* each machine needs to have 2 core CPU and 2GB memory.
```
01 # hostnamectl set-hostname yichien1-1
02 # hostnamectl set-hostname yichien1-1
03 # hostnamectl set-hostname yichien1-1
```
### ð ç¬¬äºæ­¥ : æåè¨­å® IPv4
* ç¬¬ä¸å° (192.168.56.108)
![](pic/machine1-1.png)
* ç¬¬äºå° (192.168.56.109)
![](pic/machine1-2.png)
* ç¬¬ä¸å° (192.168.56.110)
![](pic/machine1-3.png)
### ð ç¬¬ä¸æ­¥ : éæ°éåç¶²è·¯
### ð ç¬¬åæ­¥ : ç·¨è¼¯ hosts è¨­å®æª (ä¸å°é½è¦)
```
04 # gedit /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

    192.168.56.108 yichien1-1
    192.168.56.109 yichien1-2
    192.168.56.110 yichien1-3
```
## ð å®è£
### ð æååµå»ºç¨æ¶
```
05 # groupadd prometheus
06 # useradd -g prometheus -m -d /var/lib/prometheus -s /sbin/nologin prometheus
```
### ð PROMETHEUS - PROMETHEUS SERVER
```
07 # cd /home/user/Downloads
08 # wget https://github.com/prometheus/prometheus/releases/download/v2.35.0/prometheus-2.35.0.linux-amd64.tar.gz
    --2022-04-27 11:27:46--  https://github.com/prometheus/prometheus/releases/download/v2.35.0/prometheus-2.35.0.linux-amd64.tar.gz
    Resolving github.com (github.com)... 52.69.186.44
    Connecting to github.com (github.com)|52.69.186.44|:443... connected.
09 # mkdir /opt/module
10 # tar xf prometheus-2.35.0.linux-amd64.tar.gz -C /opt/module
11 # chown -R prometheus.prometheus /opt/module/prometheus-2.35.0.linux-amd64
12 # cd /opt/module
13 # mv prometheus-2.35.0.linux-amd64/ prometheus
14 # gedit /usr/lib/systemd/system/prometheus.service
    [Unit]
    Description=Prometheus
    After=network-online.target

    [Service]
    Type=simple
    Restart=on-failure
    ExecStart=/opt/module/prometheus/prometheus --config.file=/opt/module/prometheus/prometheus.yml --storage.tsdb.path=/opt/module/prometheus/data --storage.tsdb.retention=30d --web.enable-lifecycle --log.level=debug

    [Install]
    WantedBy=multi-user.targe
15 # systemctl daemon-reload
16 # systemctl start prometheus.service
```
### ð NODE_EXPORTER - PROMETHEUS SERVER + PROMETHEUS CLIENT
```
17 # cd /home/user/Downloads
18 # wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
    --2022-04-27 11:44:25--  https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
    Resolving github.com (github.com)... 52.69.186.44
    Connecting to github.com (github.com)|52.69.186.44|:443... connected.
19 # mkdir /opt/module
20 # tar xf node_exporter-1.3.1.linux-amd64.tar.gz -C /opt/module
21 # chown -R prometheus.prometheus /opt/module/node_exporter-1.3.1.linux-amd64
22 # cd /opt/module
23 # mv node_exporter-1.3.1.linux-amd64/ node_exporter
24 # gedit /usr/lib/systemd/system/node_exporter.service
    [Unit]
    Description=node_exporter
    Documentation=https://github.com/prometheus/node_exporter
    After=network.target
    
    [Service]
    Type=simple
    ExecStart=/opt/module/node_exporter/node_exporter
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
25 # systemctl daemon-reload
26 # systemctl start node_exporter.service
```
* PROMETHEUS é£ç·
![](pic/prometheus_connect.png)
### ð PROMETHEUS SERVER - ç£æ§ä»»åè¨­å®
```
27 # gedit /opt/module/prometheus/prometheus.yml 
    scrape_configs:
    - job_name: "prometheus"
        static_configs:
        - targets: ["localhost:9090"]

    - job_name: "node_exporter1"
        static_configs:
        - targets: ["192.168.56.108:9100"]

    - job_name: "node_exporter2"
        static_configs:
        - targets: ["192.168.56.109:9100","192.168.56.110:9100"]
28 # systemctl daemon-reload
29 # systemctl restart prometheus.service
```
* NODE-EXPORTER é£ç·
![](pic/node_exporter_connect.png)

## ð è£åè³æ
* [PROMETHEUS å®è£æª](https://prometheus.io/download/)
* [Prometheus å­¸ç¿éç½®](https://www.codeleading.com/article/10525895320/)
* [åæè©³è§£Prometheusç£æ§+Grafana+Alertmanageråè­¦å®è£ä½¿ç¨](https://codingnote.cc/zh-tw/p/150392/)
* [éç¸®æ¯](https://zh.wikipedia.org/wiki/%E9%9B%86%E7%B8%AE%E6%AF%94)
* [PT - Penetration test](https://zh.wikipedia.org/wiki/%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95)
* [HA - high availability é«å¯ç¨æ§](https://zh.wikipedia.org/wiki/%E9%AB%98%E5%8F%AF%E7%94%A8%E6%80%A7)
* [STP -Spanning Tree Protocol çææ¨¹åå®](https://zh.wikipedia.org/wiki/%E7%94%9F%E6%88%90%E6%A0%91%E5%8D%8F%E8%AE%AE)
* [äº¤ææ©å ç](https://www.796t.com/content/1550147965.html)


ðï¸ editor : yi-chien Liu





