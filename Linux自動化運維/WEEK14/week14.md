# ๐ Linux็ณป็ตฑ่ชๅๅ้็ถญ็ฌฌๅๅ้ฑ็ญ่จ20220518
# ๐ GRAFANA ALERT
## ๐ Docker ๅฎ่ฃ Grafana 
```
01 # systemctl start docker
02 # docker run -d --name=grafana -p 3000:3000 grafana/grafana:7.5.15
    Unable to find image 'grafana/grafana:7.5.15' locally
    Trying to pull repository docker.io/grafana/grafana ... 
    7.5.15: Pulling from docker.io/grafana/grafana
```
## ๐ Line Notify ่จญๅฎ
1. Alerting -> Notification channels -> New channel
2. ๅจ [Line Notify](https://notify-bot.line.me/zh_TW/) ็ผ่กๆฌๆ
3. ๅ ๅฅๆฌๆๅฐ Grafana
![](pic/linenotify_setting.png)
## ๐ Grafana ่จญๅฎ
1. Metrics ้ธๆ็ฃๆงๆไปค
![](pic/dashboard_setting1.png)
2. ่จญๅฎ Dashboard ็ Alert ๆขไปถ
    > `WHEN last () OF query (A, 5m, now) IS BELOW 0.9`
![](pic/dashboard_setting2.png)
3. ้ธๆ Send to ้ธๆ linenotify
![](pic/dashboard_setting3.png)
## ๐ ๆธฌ่ฉฆ็ตๆ
* ๆถๅฐ linenotify ่จๆฏ
![](pic/linenotify.png)
# ๐ ANSIBLE
## ๐ Ansible ๅฎ่ฃ
```
03 # yum install epel-release -y
04 # yum install ansible
```
## ๐ Ansible ้็ฝฎ
```
05 # gedit /etc/ansible/hosts
    [server1]
    192.168.56.109
    [server2]
    192.168.56.110
    [servers]
    192.168.56.109
    192.168.56.110
```
## ๐ SSH ้้ฐ่จญๅฎ
```
06 # ssh-keygen
07 # ssh-copy-id root@192.168.56.109
08 # ssh-copy-id root@192.168.56.110
```
## ๐ ้ๅ SSHD (192.168.56.109 / 192.168.56.110)
```
09 # systemctl start sshd
```
## ๐ ๆธฌ่ฉฆไธปๆฉ็้ฃ้ๆง
```
10 # ansible server1 -m ping
    192.168.56.109 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
11 # ansible server2 -m ping
    192.168.56.110 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
12 # ansible servers -m ping
    192.168.56.110 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
    192.168.56.109 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
```
## ๐ ๆชข่ฆ IP
```
13 # ansible all --list
    hosts (2):
        192.168.56.109
        192.168.56.110
```
## ๐ ๆชขๆฅ็ฏ้ป็่จๆถ้ซๆๆณ (commandๆฏ้ ่จญๅฏไปฅ็็ฅ)
```
14 # ansible servers -m command -a "free -m " 
    192.168.56.109 | CHANGED | rc=0 >>
                 total        used        free      shared  buff/cache   available
    Mem:           1837         642         548          16         646        1031
    Swap:          2047           0        2047
    192.168.56.110 | CHANGED | rc=0 >>
                total        used        free      shared  buff/cache   available
    Mem:           1837         804         320          16         712         833
    Swap:          2047           0        2047
```
## ๐ ๆฅ่ฉข็ณป็ตฑ่ฒ ่ผ่ณ่จ
```
15 # ansible servers -m command -a "uptime"
    192.168.56.109 | CHANGED | rc=0 >>
    16:21:29 up 28 min,  3 users,  load average: 0.00, 0.01, 0.13
    192.168.56.110 | CHANGED | rc=0 >>
    16:21:28 up 28 min,  3 users,  load average: 0.01, 0.07, 0.14
```
## ๐ ้ ็ซฏๆไปค
```
16 # ansible server1 -m command -a "cd /home/user" & "ls"      //ๅฎ่ก
    [1] 15520
    2.txt  Desktop	Documents  Downloads  Music  Pictures  Public  Templates  testdir  Videos
17 # ansible server1 -m shell -a "cd /home/user; ls"      //ๅฏๅค่ก
    192.168.56.109 | CHANGED | rc=0 >>
    Desktop
    Documents
    Downloads
    Music
    Pictures
    Public
    Templates
    testdir
    Videos
```

## ๐ ่ฃๅ่ณๆ
* [ๅคชๅฒๅฎณไบ๏ผ็ตๆผๆไบบ่ฝๆAnsible่ฌ็ๆๆ็ฝ็ฝไบ๏ผๅปบ่ญฐๆถ่](https://tw511.com/a/01/32123.html)



๐๏ธ editor : yi-chien Liu