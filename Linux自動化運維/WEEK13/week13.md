# ð Linuxç³»çµ±èªååéç¶­ç¬¬åä¸é±ç­è¨20220511
# ð PUSHGATEWAY ç£æ§
## ð METRIC ç£æ§
### ð ç¯ä¾ç¨å¼
```
echo "some_metric 9.99" | curl --data-binary @- http://192.168.56.108:9091/metrics/job/some_job
```
### ð ä½¿ç¨è³æ¬ç£æ§ (tcp_waiting_connection)
```
01 # mkdir -p /app/scripts/pushgateway
02 # gedit  /app/scripts/pushgateway/gedit tcp_waiting_connection.sh
    #!/bin/bash
    # For waiting connections
    label="count_netstat_wait_connetions"
    count_netstat_wait_connetions=`netstat -an | grep -i wait | wc -l`
    echo "$label:$count_netstat_wait_connetions"
    echo "$label $count_netstat_wait_connetions" | curl --data-binary @- http://192.168.56.108:9091/metrics/job/pushgateway/instance/$instance_name
03 # chmod +x /app/scripts/pushgateway/tcp_waiting_connection.sh
04 # ./tcp_waiting_connection.sh
    count_netstat_wait_connetions:0
```
* ææ
![](pic/tcp_waiting_connection.png)
### ð ä½¿ç¨è³æ¬ç£æ§ (check_httpd_yichien1-1)
```
05 # gedit /app/scripts/pushgateway/check_httpd_yichien1-1.sh 
    #!/bin/bash
    # For check_httpd_yichien1-1
    label="status_httpd_yichien1_1"     //ä¸è½åºç¾"-"æåºé¯
    curl -q /dev/null http://192.168.56.108 >/dev/null 2>&1   //-q:å®éæ¨¡å¼
    status=`echo $?`     //å¤æ·åé¢æ¯å¦æèª¤(1:é¯èª¤ 0:æ­£ç¢º)
    if [ $status -eq 0 ];then
        result=1
    else
        result=0
    fi
    echo "$label:$result"
    echo "$label $result" | curl --data-binary @- http://192.168.56.108:9091/metrics/job/$label
06 # chmod +x /app/scripts/pushgateway/check_httpd_yichien1-1.sh 
07 # ./check_httpd_yichien1-1.sh 
    status_httpd_yichien1_1:0
08 # systemctl start httpd
09 # ./check_httpd_yichien1-1.sh 
    status_httpd_yichien1_1:1
```
* ææ - httpd éé
![](pic/status_httpd_0.png)
* ææ - httpd éå
![](pic/status_httpd_1.png)
### ð å®æåä»½
```
10 # crontab -e 
    * * * * * /app/scripts/pushgateway/check_httpd_yichien1-1.sh 
    * * * * * sleep 15;/app/scripts/pushgateway/check_httpd_yichien1-1.sh
    * * * * * sleep 30;/app/scripts/pushgateway/check_httpd_yichien1-1.sh
    * * * * * sleep 45;/app/scripts/pushgateway/check_httpd_yichien1-1.sh
11 # crontab -l     //æ¥çåä»½æç¨
    * * * * * /app/scripts/pushgateway/check_httpd_yichien1-1.sh 
    * * * * * sleep 15;/app/scripts/pushgateway/check_httpd_yichien1-1.sh
    * * * * * sleep 30;/app/scripts/pushgateway/check_httpd_yichien1-1.sh
    * * * * * sleep 45;/app/scripts/pushgateway/check_httpd_yichien1-1.sh
```
## ð GRAFANA DASHBOARD ç£æ§
1. New Dashboard -> Add a new panel
2. å¨ä¸æ¹ç Metrics browser é¸æè¦æ¥ççé ç® 
3. æä¸ Run query å¾ï¼ä¸æ¹å³å¯é¡¯ç¤ºæ¸æ
![](pic/grafana_dashboard_create1.png)
4. Apply -> Save dashboard
![](pic/grafana_dashboard_create2.png)

# ð æä»¤è§£æ
#### ð EX. `whatis` : ç¹å®å½ä»¤çç°¡ç­æè¿°è®æª
```
12 # whatis read
    read (1)             - bash built-in commands, see bash(1)
    read (1p)            - read a line from standard input
    read (2)             - read from a file descriptor
    read (3p)            - read from a file
13 # whatis cat
    cat (1)              - concatenate files and print on the standard output
    cat (1p)             - concatenate and print files
```
#### ð EX. `cat`
> DESCRIPTIONã<br>
> Concatenate FILE(s), or standard input, to standard output.
* æéä¸è¬æªæ¡è®åä¸¦é¡¯ç¤ºåºä¾
```
14 # cat /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

    192.168.56.108 yichien1-1
    192.168.56.109 yichien1-2
    192.168.56.110 yichien1-3
```
* ææªæ¡å°åå°æ¨æºè¼¸å¥ä¸¦è®åæ¨æºè¼¸å¥
```
15 # cat </etc/hosts    (cat 0</etc/hosts)
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

    192.168.56.108 yichien1-1
    192.168.56.109 yichien1-2
    192.168.56.110 yichien1-3
```
#### ð EX. `read` 
* è®åæ¨æºè¼¸å¥
```
16 # read -p "what is your name:" name
    what is your name:yichien
17 # echo $name
    yichien
```
#### ð EX. `EOF` : End Of Fileçç¸®å¯«ï¼å¨æå­çæå¾å­å¨æ­¤å­åè¡¨ç¤ºè³æçµæ
* è®åå¤è¡æ¨æºè¼¸å¥
```
18 # cat <<EOF
    > hello
    > world
    > EOF
    hello
    world
``` 
* å¤è¡æ¨æºè¼¸å¥å¾éæ°å®ç¾©ä½ç½®
```
19 # cat <<EOF > /tmp/a.txt
    > hello
    > world
    > 123
    > 456
    > 789
    > EOF
20 # cat /tmp/a.txt 
    hello
    world
    123
    456
    789
```
* æå­ä¸²ä¸å°æ¨æºè¼¸åºå¾éæ°å®ç¾©ä½ç½®
```
21 # cat <<< "hi" >/tmp/a.txt 
22 # cat <<< "hi"
    hi
```
* `/dev/pts/0` : æ¨æºè¼¸å¥(stdin 0) æ¨æºè¼¸åº(stdout 1) æ¨æºé¯èª¤(stderr 2)
![](pic/standard.png)
```
23 # ls -l /proc/self/fd
    total 0
    lrwx------ 1 root root 64 May 16 16:14 0 -> /dev/pts/0
    lrwx------ 1 root root 64 May 16 16:14 1 -> /dev/pts/0
    lrwx------ 1 root root 64 May 16 16:14 2 -> /dev/pts/0
    lr-x------ 1 root root 64 May 16 16:14 3 -> /proc/15589/fd
```
#### ð EX. `touch` 
* å©å±¤å¼å»ºç«æªæ¡
```
24 # touch {a..c}{1..5}.txt
25 # ls
    a       a2.txt  a4.txt  b1.txt  b3.txt  b5.txt  c2.txt  c4.txt  hello.txt
    a1.txt  a3.txt  a5.txt  b2.txt  b4.txt  c1.txt  c3.txt  c5.txt  test
```
* é¡å¤echoé²åæªæ¡
```
26 # echo "1" >> 1.txt
27 # echo "2" >> 1.txt
28 # echo "3" >> 1.txt
29 # cat 1.txt 
    1
    2
    3
```
* èª¿ç¨å­é²ç¨
```
30 # exec > 2.txt
31 # echo "1"
32 # echo "2"
33 # echo "3"
34 # exec > /dev/pts/0     //åå¾©é²ç¨
35 # cat 2.txt 
    1
    2
    3
```
#### ð EX. `grep` 
* æ­£åè¡¨éå¼
```
36 # ps -ef | grep prometheus
    root      6506     1  0 15:21 ?        00:00:38 /opt/module/prometheus/prometheus --config.file=/opt/module/prometheus/prometheus.yml --storage.tsdb.path=/opt/module/prometheus/data --storage.tsdb.retention=30d --web.enable-lifecycle --log.level=debug
    root     18507  3239  0 21:56 pts/0    00:00:00 grep --color=auto prometheus
37 # ps -ef | grep prometheus | grep -v grep     //-v : æé¤å¨å¤
    root      6506     1  0 15:21 ?        00:00:38 /opt/module/prometheus/prometheus --config.file=/opt/module/prometheus/prometheus.yml --storage.tsdb.path=/opt/module/prometheus/data --storage.tsdb.retention=30d --web.enable-lifecycle --log.level=debug
38 # ps -ef | grep prometheu[s]
    root      6506     1  0 15:21 ?        00:00:37 /opt/module/prometheus/prometheus --config.file=/opt/module/prometheus/prometheus.yml --storage.tsdb.path=/opt/module/prometheus/data --storage.tsdb.retention=30d --web.enable-lifecycle --log.level=debug
39 # ps -ef | grep [p]rometheus
    root      6506     1  0 15:21 ?        00:00:37 /opt/module/prometheus/prometheus --config.file=/opt/module/prometheus/prometheus.yml --storage.tsdb.path=/opt/module/prometheus/data --storage.tsdb.retention=30d --web.enable-lifecycle --log.level=debug
```

## ð è£åè³æ
* [ãprometheusç³»åã7ãpushgatewayèªå®ä¹èæ¬ééçæ§æ°æ®](https://blog.51cto.com/root/3033785)
* [Prometheus ä»ç´¹èåºç¤å¥é (ä¸)](https://www.inwinstack.com/zh/blog-tw/blog_other-tw/2156/)
* [Exec - Linuxå½ä»¤ - UNIXå½ä»¤](https://zhtw.eyewated.com/exec-linux%E5%91%BD%E4%BB%A4-unix%E5%91%BD%E4%BB%A4/)



ðï¸ editor : yi-chien Liu