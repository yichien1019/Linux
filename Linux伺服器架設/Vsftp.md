# ð VSFTP
## ð å®è£
```
01 # yum install vsftpd -y
02 # systemctl start vsftpd
03 # systemctl status vsftpd
04 # netstat -tunlp | grep 21
    tcp6       0      0 :::80                   :::*                    LISTEN      5021/httpd          
    tcp6       0      0 :::21                   :::*                    LISTEN      6660/vsftpd
05 # systemctl stop firewalld
06 # gedit /etc/vsftpd/vsftpd.conf
    anon_upload_enable=YES     (#åªé¤)
    anon_mkdir_write_enable=YES     (#åªé¤)
```
### ð å¶ä»æä»¤
| ls | ååºç®åç®éä¸çæªæ¡æ¸å®    (åå§ä½ç½®/var/ftp) |
| --- | --- |
| cd | åæç®é |
| get | ä¸è¼ä¼ºæå¨ä¸çæªæ¡è³ç¨æ¶ç«¯ |
| put | å°ç¨æ¶ç«¯çæªæ¡ä¸å³è³ä¼ºæå¨ä¸ |
| asc | è¨­å®å³è¼¸æ ¼å¼çºASCIIæå­æ¨¡å¼ |
| bin | è¨­å®å³è¼¸æ ¼å¼çºäºé²å¶æ¨¡å¼binaryæ¨¡å¼ |
| mkdir | å¨ä¼ºæå¨ä¸å»ºç«æ°ç®é |
### ð æå­æ¨¡å¼é£ç· (windows cmd)
```
C:\Users\yichien>ftp 192.168.56.5
å·²é£ç·å° 192.168.56.5ã
220 (vsFTPd 3.0.2)
200 Always in UTF8 mode.
ä½¿ç¨è (192.168.56.5:(none)): anonymous
331 Please specify the password.
å¯ç¢¼:
230 Login successful.
ftp> bin            //å³è¼¸åè¦ååæ
200 Switching to Binary mode.
ftp> get a
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for a (0 bytes).
226 Transfer complete.
ftp> mget a*        //ä¸è¼å¤åæªæ¡
200 Switching to ASCII mode.
mget a1? y
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for a1 (0 bytes).
226 Transfer complete.
ftp> prompt off     //è©¢åæ¨¡å¼éé
äºåæ¨¡å¼ éé ã
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
a1
a2
a3
226 Directory send OK.
ftp> put a
200 PORT command successful. Consider using PASV.
550 Permission denied.     //éæ¬échmod 777 & æ´æ¹èæ¬æ©/etc/vsftpd/vsftpd.conf
ftp> put a
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
```
### ð åå½¢æ¨¡å¼é£ç· (WinSCP)
![](pic/picdownload.jpg)
### ð ä½¿ç¨èå¯ä»¥ç»å¥ä¸å¯ä»¥åæç®é
```
07 # gedit /etc/vsftpd/vsftpd.conf
    chroot_local_user=YES     (#åªé¤)
    allow_writeable_chroot=YES     (èªè¡è¼¸å¥)
```
### ð éå¶ä½¿ç¨èç»å¥
```
08 # gedit /etc/vsftpd/user_list
    # vsftpd userlist
    # If userlist_deny=NO, only allow users in this file
    # If userlist_deny=YES (default), never allow users in this file, and
    # do not even prompt for a password.
    # Note that the default vsftpd pam config also checks /etc/vsftpd/ftpusers
    # for users that are denied.
    root
    bin
    daemon
    adm
    lp
    sync
    shutdown
    halt
    mail
    news
    uucp
    operator
    games
    nobody
    tom     (èªè¡è¼¸å¥ä½¿ç¨è)
```
### ð åè©¦ä½¿ç¨éå¶ä½¿ç¨èç»å¥ 
```
09 # gedit /etc/httpd/conf/httpd.conf
    <Directory "/var/www/html">
        Order allow,deny
        Allow from all             åè¨±ææé£æ¥
        Deny from 192.168.56.103   é¤äºéä¸åIP
10 # ftp 192.168.56.5
    Connected to 192.168.56.5 (192.168.56.5).
    220 (vsFTPd 3.0.2)
    Name (192.168.56.5:root): tom
    530 Permission denied.
    Login failed.
```


ðï¸ editor : yi-chien Liu

