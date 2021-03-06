# 📝 BIND
```markdown
01 # dig @8.8.8.8 www.nqu.edu.tw

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.8 <<>> @8.8.8.8 www.nqu.edu.tw
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25329
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 512
    ;; QUESTION SECTION:
    ;www.nqu.edu.tw.			IN	A

    ;; ANSWER SECTION:
    www.nqu.edu.tw.		20912	IN	A	203.72.226.40

    ;; Query time: 16 msec
    ;; SERVER: 8.8.8.8#53(8.8.8.8)
    ;; WHEN: Fri Dec 31 15:51:51 CST 2021
    ;; MSG SIZE  rcvd: 59
02 # dig www.nqu.edu.tw

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.8 <<>> www.nqu.edu.tw
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60373
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 1232
    ;; QUESTION SECTION:
    ;www.nqu.edu.tw.			IN	A

    ;; ANSWER SECTION:
    www.nqu.edu.tw.		43200	IN	A	203.72.226.40

    ;; Query time: 3424 msec
    ;; SERVER: 9.9.9.9#53(9.9.9.9)
    ;; WHEN: Fri Dec 31 16:15:20 CST 2021
    ;; MSG SIZE  rcvd: 59
03 # /etc/resolv.conf
; generated by /usr/sbin/dhclient-script
nameserver 10.10.10.3
nameserver 9.9.9.9
```

### 👉 DIG 介紹
`dig @[名稱伺服器位置] 名稱 [查詢類型]`

| 參數 | 用法 |
| --- | --- |
a | 查 IP 位址
mx | 查郵件伺服器
ns | 查名稱伺服器
cname | 查別名
ptr | 由 IP 位址反查名稱
hinfo | DNS 伺服器的系統資訊
### 👉 nslookup
```markdown
04 # nslookup www.nqu.edu.tw
    Server:		9.9.9.9
    Address:	9.9.9.9#53

    Non-authoritative answer:
    Name:	www.nqu.edu.tw
    Address: 203.72.226.40
05 # nslookup www.nqu.edu.tw 8.8.8.8
    Server:		8.8.8.8
    Address:	8.8.8.8#53

    Non-authoritative answer:
    Name:	www.nqu.edu.tw
    Address: 203.72.226.40
```

## 🔖 安裝DNS Server

```markdown
06 # systemctl stop firewalld
07 # getenforce
    Disabled
08 # yum install bind bind-chroot bind-utils
```

### 👉 Windows查詢虛擬機IP
* 先dig → host → gedit → restart
```markdown
09 # dig @127.0.0.1 www.nqu.edu.tw

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.8 <<>> @127.0.0.1 www.nqu.edu.tw
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5922
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 3

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;www.nqu.edu.tw.			IN	A

    ;; ANSWER SECTION:
    www.nqu.edu.tw.		259200	IN	A	203.72.226.40

    ;; AUTHORITY SECTION:
    nqu.edu.tw.		260	IN	NS	ns1.nqu.edu.tw.
    nqu.edu.tw.		260	IN	NS	ns2.nqu.edu.tw.

    ;; ADDITIONAL SECTION:
    ns2.nqu.edu.tw.		260	IN	A	203.72.226.2
    ns1.nqu.edu.tw.		260	IN	A	59.120.127.182

    ;; Query time: 2072 msec
    ;; SERVER: 127.0.0.1#53(127.0.0.1)
    ;; WHEN: Sat Jan 01 03:05:51 CST 2022
    ;; MSG SIZE  rcvd: 127
10 # host -t A www.nqu.edu.tw 127.0.0.1
    Using domain server:
    Name: 127.0.0.1
    Address: 127.0.0.1#53
    Aliases: 

    www.nqu.edu.tw has address 203.72.226.40
11 # gedit /etc/named.conf
    options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { ::1; };
        directory 	"/var/named";
        dump-file 	"/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file  "/var/named/data/named.recursing";
        secroots-file   "/var/named/data/named.secroots";
        allow-query     { any; };
12 # systemctl restart named
```
* 連線
```markdown
C:\Users\yichien>nslookup www.nqu.edu.tw 192.168.56.5
伺服器:  UnKnown
Address:  192.168.56.5

未經授權的回答:
名稱:    www.nqu.edu.tw
Address:  203.72.226.40
```

### 👉 Windows 無法連線 本地端可以連線
```markdown
13 # gedit /etc/named.conf
options {
    listen-on port 53 { 127.0.0.1; };
    listen-on-v6 port 53 { ::1; };
    directory 	"/var/named";
    dump-file 	"/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    recursing-file  "/var/named/data/named.recursing";
    secroots-file   "/var/named/data/named.secroots";
    allow-query     { localhost; };
```
* 連線
```markdown
C:\Users\yichien>nslookup www.nqu.edu.tw 192.168.56.5
伺服器:  UnKnown
Address:  192.168.56.5

DNS request timed out.
    timeout was 2 seconds.
```

## 🔖 管理網域
### 👉 正向解析

```markdown
14 # gedit /etc/named.rfc1912.zones
    zone "a.com" IN {
            type master;
            file "a.com.zone";
            allow-update { none; };
    };
15 # cd /var/named
16 # ls
    chroot  data  dynamic  named.ca  named.empty  named.localhost  named.loopback  slaves
17 # gedit a.com.zone
    $TTL 600 ;10 minutes

    @ IN SOA		@ yichien1019.gmail.com (
            2021031803 ;serial
            10800	   ;refresh
            900	   ;retry
            604800	   ;epxire
            86400	   ;minimum
            )
    @		NS	dns1.a.com.
    dns.com		A	192.168.56.5
    dns1		A	192.168.56.5
    www		A	192.168.56.150
    eshop 		CNAME	www
    ftp		A	192.168.56.150
    abc		A	192.168.56.120
```    
#### 📍 結果
```markdown
18 # host -t ns a.com 127.0.0.1
    Using domain server:
    Name: 127.0.0.1
    Address: 127.0.0.1#53
    Aliases: 

    a.com name server dns1.a.com.
19 # host -t A ftp.a.com 127.0.0.1
    Using domain server:
    Name: 127.0.0.1
    Address: 127.0.0.1#53
    Aliases: 

    ftp.a.com has address 192.168.56.150
20 # host -t ns a.com 127.0.0.1
    Using domain server:
    Name: 127.0.0.1
    Address: 127.0.0.1#53
    Aliases: 

    a.com name server dns1.a.com.
21 # host -t CNAME eshop.a.com 127.0.0.1
    Using domain server:
    Name: 127.0.0.1
    Address: 127.0.0.1#53
    Aliases: 

    eshop.a.com is an alias for www.a.com.
```
### 👉 反向解析

```markdown
22 # gedit /etc/named.rfc1912.zones
    zone "56.168.192.in-addr.arpa" IN {
        type master;
        file "56.168.192.in-addr.arpa.zone";
        allow-update { none; };
    };
23 # cd /var/named
24 # ls
    56.168.192.in-addr.arpa.zone  chroot  dynamic   named.empty      named.loopback
    a.com.zone                    data    named.ca  named.localhost  slaves
25 # gedit 56.168.192.in-addr.arpa.zone
    $TTL 600

    @ IN SOA       @ yichien1019.gmail.com (
                    2021031803 ;serial
                    10800      ;refresh
                    900        ;retry
                    604800     ;epxire
                    86400      ;minimux
                    )
    56.168.192.in-addr.arpa.   IN  NS dns1.a.com.
    56.168.192.in-addr.arpa.   IN  NS dns2.a.com.
    200.56.168.192.in-addr.arpa. IN PTR www.a.com.
    150.56.168.192.in-addr.arpa. IN PTR ftp.a.com.
```
#### 📍 結果
```markdown
26 # nslookup 192.168.56.200 127.0.0.1
    200.56.168.192.in-addr.arpa	name = www.a.com.
27 # nslookup 192.168.56.150 127.0.0.1
    150.56.168.192.in-addr.arpa	name = ftp.a.com.
```
### 👉 檢查錯誤
```markdown
28 # named-checkconf /etc/named.conf
29 # named-checkzone a.com /var/named/a.com.zone
30 # named-checkzone a.com /var/named/a.com.zone
    zone a.com/IN: loaded serial 2021031803
    OK
```


🖊️ editor : yi-chien Liu