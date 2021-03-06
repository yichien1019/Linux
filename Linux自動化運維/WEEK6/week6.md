# ð Linuxç³»çµ±èªååéç¶­ç¬¬å­é±ç­è¨20220323
# ð RSYNC (é ç«¯åæ­¥)
åæ¸ | å§å®¹
--- | ---
-v | verbose æ¨¡å¼ï¼è¼¸åºæ¯è¼è©³ç´°çè¨æ¯
-r | éè¿´ï¼recursiveï¼åä»½ææå­ç®éä¸çç®éèæªæ¡
-a | å°è£åä»½æ¨¡å¼ï¼ç¸ç¶æ¼ -rlptgoDï¼éè¿´åä»½ææå­ç®éä¸çç®éèæªæ¡ï¼ä¿çé£çµæªãæªæ¡çææèãç¾¤çµãæ¬éä»¥åæéæ³è¨
-z | åç¨å£ç¸®
-h | å°æ¸å­ä»¥æ¯è¼å®¹æé±è®çæ ¼å¼è¼¸åº
### ð å®è£ rsync
```
01 # yum install rsync
```
## ð rsync æä½
### ð ä¿®æ¹ãæ°å¢æ¬å°ç«¯çæªæ¡ & ç®é
* éç¨®ç¨æ³å°æ¼æªæ¡æç®éé½é©ç¨
#### ð åä»½æªæ¡
```
02 # rsync -avh a.txt /tmp
    sending incremental file list
    a.txt

    sent 94 bytes  received 35 bytes  258.00 bytes/sec
    total size is 3  speedup is 0.02
03 # cd /tmp
04 # ls | grep a.txt
    a.txt
05 # rsync -avh testdir/ /tmp
    sending incremental file list
    ./
    1.txt
    2.txt
    3.txt
    4.txt
    5.txt

    sent 591 bytes  received 209 bytes  1.60K bytes/sec
    total size is 0  speedup is 0.00
```
* ææ
![](pic/rsync.png)
#### ð åä»½ç®é
```
06 # rsync -avh testdir/ /tmp/testdir
    sending incremental file list
    ./
    a/
    a/b/
    a/b/c/

    sent 257 bytes  received 31 bytes  576.00 bytes/sec
    total size is 6  speedup is 0.02
```
* ææ
![](pic/rsync_content.png)
#### ð æ¥çéæ¹å¼(ç¨ä¸ç¡äº)
```
07 # md5sum 1.txt
    d41d8cd98f00b204e9800998ecf8427e  1.txt
08 # echo "11" > 1.txt
09 # md5sum 1.txt
    166d77ac1b46a1ec38aa35ab7e628ab5  1.txt
```
### ð åªé¤æ¬å°ç«¯æªæ¡ `rsync -avh --delete`
```
10 # rm -rf {13..15}.txt
11 # cd ..
12 # rsync -avh --delete testdir/ /tmp/testdir
    sending incremental file list
    deleting 15.txt
    deleting 14.txt
    deleting 13.txt
    ./

    sent 180 bytes  received 49 bytes  458.00 bytes/sec
    total size is 6  speedup is 0.03
```
### ð åä»½é ç«¯æªæ¡ & ç®é
```
13 # rsync -avzh testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    ./
    1.txt
    2.txt
    3.txt
    4.txt
    5.txt
    a/
    a/b/
    a/b/c/

    sent 417 bytes  received 130 bytes  121.56 bytes/sec
    total size is 6  speedup is 0.01
```
* ææ
![](pic/rsync_avzh.png)
#### ð éå¶ç¶²è·¯é »å¯¬
```
14 # rsync -avzh --bwlimit=100K testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    ./
    13.txt

    sent 317 bytes  received 41 bytes  716.00 bytes/sec
    total size is 6  speedup is 0.02
```
* ææ
![](pic/rsync_avzh_bwlimit.png)
#### ð èªè¨ SSH é£æ¥å 
```
15 # rsync -avzh -e "ssh -p 22" testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    ./
    14.txt

    sent 338 bytes  received 41 bytes  758.00 bytes/sec
    total size is 6  speedup is 0.02
```
* ææ
![](pic/rsync_avzh_port.png)
#### ð é¡¯ç¤ºå³è¼¸é²åº¦
```
16 # rsync -avzh --progress testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    ./
    15.txt
                0 100%    0.00kB/s    0:00:00 (xfr#1, to-chk=7/14)

    sent 357 bytes  received 41 bytes  796.00 bytes/sec
    total size is 6  speedup is 0.02
```
* ææ
![](pic/rsync_avzh_progress.png)
#### ð åæ­¥åªé¤æªæ¡
```
17 # rsync -avzh --delete testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    deleting 15.txt
    deleting 14.txt
    deleting 13.txt
    deleting 12.txt
    deleting 11.txt
    ./

    sent 214 bytes  received 72 bytes  190.67 bytes/sec
    total size is 6  speedup is 0.02
```
* ææ
![](pic/rsync_avzh_delete.png)

#### ð åä»½ç¹å®æªæ¡
```
18 # rsync -avzh --exclude '*.txt' testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    ./
    test/

    sent 149 bytes  received 26 bytes  350.00 bytes/sec
    total size is 0  speedup is 0.00
```
* ææ
![](pic/rsync_avzh_exclude.png)
#### ð éå®åä»½æªæ¡å¤§å°
```
19 # rsync -avzh --max-size=1M testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    size.txt

    sent 321 bytes  received 39 bytes  720.00 bytes/sec
    total size is 18  speedup is 0.05
```
* ææ
![](pic/rsync_avzh_size.png)
#### ð èªååªé¤ä¾æºæªæ¡
* å¨åä»½æªæ¡ä¹å¾ï¼èªåå°ä¾æºæªæ¡åªé¤ï¼ä¹å°±æ¯ç¸ç¶æ¼ mv çææï¼
```
20 # rsync -avzh --remove-source-files testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    ./
    11.txt
    12.txt
    13.txt

    sent 431 bytes  received 152 bytes  1.17K bytes/sec
    total size is 18  speedup is 0.03
```
* ææ
![](pic/rsync_avah_remove.png)
#### ð æ¸¬è©¦ rsync åæ¸
* å¦æä¸ç¢ºå®èªå·±ç `rsync` åæ¸æ¯å¦æ­£ç¢ºï¼å¨å¯¦éå·è¡ä¹åå¯ä»¥å ä¸ `--dry-run` ä¾æ¸¬è©¦ä¸ä¸ï¼å ä¸éååæ¸ä¹å¾æè¼¸åºæ­£å¸¸çè¨æ¯ï¼ä¸éä¸¦ä¸ææ´åå°ä»»ä½çæªæ¡
```
21 # rsync -avzh --dry-run --remove-source-files testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    ./
    15.txt
    16.txt
    17.txt
    18.txt
    19.txt
    20.txt

    sent 278 bytes  received 41 bytes  638.00 bytes/sec
    total size is 0  speedup is 0.00 (DRY RUN)
```
* ææ
![](pic/rsync_avah_dryrun.png)
#### ð é¡¯ç¤ºæªæ¡è®åè³è¨
```
22 # rsync -avzhi --existing testdir/ root@192.168.56.107:/tmp/backup
    sending incremental file list
    <f.st...... 3.txt

    sent 319 bytes  received 45 bytes  242.67 bytes/sec
    total size is 4  speedup is 0.01
```
![](pic/rsync_avahi.png)
## ð æ¨ç¤ºå­ä¸²çæ¬ä½
åæ¸ | å§å®¹
--- | ---
Y | < ä»£è¡¨æªæ¡å³éè³é ç«¯ï¼> ä»£è¡¨æªæ¡å³éè³æ¬å°ç«¯ï¼c ä»£è¡¨æ¬å°ç«¯è®åï¼å»ºç«ç®éç­ï¼ï¼h ä»£è¡¨ç¡¬å¼é£çµï¼hard linkï¼ï¼. ä»£è¡¨æ²æè®åï¼* ä»£è¡¨å¶é¤æ¬ä½æåå«è¨æ¯ï¼ä¾å¦ deletingï¼ã
X | æªæ¡é¡åï¼f çºä¸è¬æªæ¡ï¼d çºç®éï¼L çºé£çµæªï¼D çºè¨­åæªï¼deviceï¼ï¼S çºç¹æ®æªæ¡ï¼å¦ sockets æ fifoï¼ã
c | ä»£è¡¨æªæ¡å§å®¹æè®åã
s | ä»£è¡¨æªæ¡å¤§å°æè®åã
t | ä»£è¡¨æªæ¡æéæ³è¨æè®åã
p | ä»£è¡¨æªæ¡æ¬éæè®åã
o | ä»£è¡¨æªæ¡ææèæè®åã
g | ä»£è¡¨æªæ¡ç¾¤çµæè®åã
u | ä¿çæ¬ä½ã
a | ä»£è¡¨æªæ¡ ACL è³è¨æè®åã
x | ä»£è¡¨æªæ¡æ´åå±¬æ§ï¼extended attributeï¼æè®åã

# ð å®è£ Rsyncd é ç«¯æªæ¡åæ­¥ä¼ºæå¨
* å©å°åæéåæ­¥
```
23 # ntpdate watch.stdtime.gov.tw
    28 Mar 14:38:34 ntpdate[6330]: step time server 118.163.81.63 offset 1.591184 sec
```
### ð ååä¼ºæå¨
```
24 # systemctl enable rsyncd
    Created symlink from /etc/systemd/system/multi-user.target.wants/rsyncd.service to /usr/lib/systemd/system/rsyncd.service.
25 # systemctl start rsyncd
```
```
26 # cat /var/run/rsyncd.pid
    12006
27 # cat /var/log/rsyncd.log
    2022/03/28 14:41:24 [12006] Unknown Parameter encountered: "secrets"
    2022/03/28 14:41:24 [12006] IGNORING unknown parameter "secrets"
    2022/03/28 14:41:24 [12006] rsyncd version 3.1.2 starting, listening on port 873
```
### ð SERVERç«¯
```
28 # gedit /etc/rsyncd.conf
    uid=root
    gid=root
    pid file = /var/run/rsyncd.pid
    log file = /var/log/rsyncd.log
    secrets file = /etc/rsyncd.passwd 

    [mod1]
    path = /backup1
    read only = no
    auth users = rsync_vuser
29 # gedit /etc/rsyncd.passwd
    rsync_vuser:123456
```
#### ð éåæ¬é & ååä¼ºæå¨
```
30 # chmod 600 /etc/rsyncd.passwd
31 # chmod 600 /etc/rsyncd.conf
32 # systemctl start rsyncd
```
### ð CLIENTç«¯
```
33 # gedit /etc/rsync_vuser.passwd
    123456
34 # chmod 600 /etc/rsync_vuser.passwd
35 # rsync -avzu --progress --password-file=/etc/rsync_vuser.passwd /tmp/test.txt rsync_vuser@192.168.56.108::mod1
    sending incremental file list
    test.txt
                6 100%    0.00kB/s    0:00:00 (xfr#1, to-chk=0/1)

    sent 97 bytes  received 35 bytes  264.00 bytes/sec
    total size is 6  speedup is 0.05
```
* ææ
![](pic/rsyncd.png)

# ð RSYNC + INOTIFYåæ­¥åä»½
### ð SERVERç«¯
#### ð ç¼è¾rysncéç½®æä»¶
```
36 # gedit /etc/rsyncd.conf
    uid = rsync
    gid = rsync
    use chroot = no
    max connections = 200
    timeout = 300
    pid file = /var/run/rsyncd.pid
    lock file = /var/run/rsync.lock
    log file = /var/log/rsyncd.log
    secrets file = /etc/rsync.passwd
    ignore errors
    read only = false
    list = false
    hosts allow = 192.168.56.0/24
    auth users = rsync_backup

    [backup]
        comment = "backup dirtory"
        path = /home/user/backup
        auth users = rsync_backup
```
#### ð åµå»ºç¨æ¶
```
37 # useradd -s /sbin/nologin -M rsync
```
#### ð åµå»ºåæ­¥ç®é
```
38 # mkdir /home/user/backup
39 # chown -R rsync.rsync backup/
```
#### ð åµå»ºç¨æ¶ & æ´æ¹å¯ç¢¼æä»¶æ¬é
```
40 # touch /etc/rsync.passwd
41 # echo "rsync_backup:backup" > /etc/rsync.passwd
42 # chmod 600 /etc/rsync.passwd
```
#### ð ååä¼ºæå¨
```
43 # systemctl restart rsyncd
```
#### ð æ¥çé²ç¨
```
44 # ps aux | grep rsync
    root      8080  0.0  0.1 114848  1184 ?        Ss   11:31   0:00 /usr/bin/rsync --daemon --no-detach
    root      8103  0.0  0.0 112812   980 pts/0    S+   11:31   0:00 grep --color=auto rsync
45 # netstat -lntp | grep rsync
    tcp        0      0 0.0.0.0:873             0.0.0.0:*               LISTEN      8080/rsync          
    tcp6       0      0 :::873                  :::*                    LISTEN      8080/rsync  
```
### ð CLIENTç«¯
#### ð æª¢æ¥æ¯å¦å®è£ rsync & inotify
```
46 # rpm -qa | grep rsync
47 # rpm -qa | grep inotify-tools
```
#### ð åµå»ºå®å¨èªè­æä»¶ & æ´æ¹å¯ç¢¼æä»¶æ¬é
```
48 # touch /etc/rsync.passwd
49 # echo "backup" > /etc/rsync.passwd
50 # chmod 600 /etc/rsync.passwd
```
#### ð æ¨éæä»¶æ¸¬è©¦
```
50 # rsync -avzP  /home/user/testdir rsync_backup@192.168.56.107::backup --password-file=/etc/rsync.passwd
```
#### ð å¯«è³æ¬ - gedit inotify_backup.sh 
```
51 # gedit inotify_backup.sh 
    #!/bin/bash
    Path=/home/user/testdir/
    backup_Server=192.168.56.107

    /usr/bin/inotifywait -mrq --format '%w%f' -e create,close_write,delete $Path  | while read line  
    do
        if [ -f $line ];then
            rsync -az $line --delete rsync_backup@$backup_Server::backup --password-file=/etc/rsync.passwd
        else
            cd $Path &&\
            rsync -az ./ --delete rsync_backup@$backup_Server::backup --password-file=/etc/rsync.passwd
        fi
    done
```
#### ð æ´æ¹æ¬é & å¾èºéè¡è³æ¬
```
52 # chmod +x /home/user/inotify_backup.sh 
52 # sh /home/user/inotify_backup.sh 
53 # touch test.txt
```
* ææ
![](pic/rsync_inotify.png)

## ð è£åè³æ
* [CentOS 7.6 ä¸å®è£ Rsyncd é ç«¯æªæ¡åæ­¥ä¼ºæå¨](https://blog.tomy168.com/2019/01/centos-76x64-rsync-daemon.html)
* [Linux ä½¿ç¨ rsync é ç«¯æªæ¡åæ­¥èåä»½å·¥å·æå­¸èç¯ä¾](https://blog.gtwang.org/linux/rsync-local-remote-file-synchronization-commands/)
* [Linux è¿ç»´ï¼rsync+inotifyå®æ¶åæ­¥](https://segmentfault.com/a/1190000018096553)


ðï¸ editor : yi-chien Liu