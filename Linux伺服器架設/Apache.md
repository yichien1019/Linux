# ð APACHE + MARIADB SERVER 
# ð APACHE
### ð å®è£ APACHE + åå§è¨­å®
```
01 # yum install httpd
02 # systemctl stop firewalld
03 # getenforce
    Disabled
    // å¦ææ²ædisabledè¦ä¿®æ¹vim /etc/selinux/configæ´æ¹å®è¦ééæ©
```
![apache.JPG](pic/apache.png)
* Linux æéç¶²é æ 127.0.0.1
* Windows æéç¶²é æ IP ä½ç½®
## ð MARIADB
### ð å®è£ MARIADB + åå§è¨­å®
```
04 # yum install mariadb-server mariadb
05 # systemctl start mariadb.service
06 # systemctl status mariadb.service
    â mariadb.service - MariaDB database server
    Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor preset: disabled)
    Active: active (running) since Fri 2021-12-03 15:28:53 CST; 12s ago
07 # mysql_secure_installation
    //password : 123456
08 # mysql -u root -p
    Enter password: 
    Welcome to the MariaDB monitor.
```
# ð MYSQL
### ð æ¥çè³æåº« `show databases;`
```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)
```
### ð åµå»ºè³æåº« `create database testdb;`

```
MariaDB [(none)]> create database testdb;
Query OK, 1 row affected (0.00 sec)
MariaDB [testdb]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
| testdb             |
+--------------------+
5 rows in set (0.00 sec)
```
### ð ä½¿ç¨è³æåº« `use testdb;`
```
MariaDB [(none)]> use testdb;
Database changed
```

### ð åµå»ºè¡¨æ ¼ `create table addrbook(name varchar(50) not null,phone varchar(10));`
```
MariaDB [testdb]> create table addrbook(name varchar(50) not null,phone varchar(10));
Query OK, 0 rows affected (0.31 sec)
```

### ð å å¥è³æ `insert into addrbook(name, phone) values ("xxx","xxx");`
```
MariaDB [testdb]> insert into addrbook(name, phone) values ("mary","0987654321");
Query OK, 1 row affected (0.30 sec)

MariaDB [testdb]> insert into addrbook(name, phone) values ("tom","1234567890");
Query OK, 1 row affected (0.29 sec)
```

### ð é¡¯ç¤ºè³æ `select * from addrbook;`
```
MariaDB [testdb]> select * from addrbook;
+------+------------+
| name | phone      |
+------+------------+
| mary | 0987654321 |
| tom  | 1234567890 |
+------+------------+
2 rows in set (0.00 sec)
```

# ð PHP

```
09 # yum install php php-mysql
10 # systemctl restart httpd
11 # cd /var/www/html
12 # ls
    hi.php  index.html  test.php
13 # gedit test.php 
    <?php
    phpinfo();
    ?>
```
![php.JPG](pic/php.png)

### ð é£çµå°è³æåº«é¡¯ç¤ºè³æ ( APACHE + MARIADB + PHP )
```
14 # cd /var/www/html
15 # gedit hi.php 
    <?php
    $servername="localhost";
    $username="root";
    $password="123456";
    $dbname="testdb";

    $conn = new mysqli($servername,$username,$password,$dbname);

    if($conn->donnect_error){
        die("connection failed:".$conn->connect_error);
    }
    //echo "connection ok"
    $sql="select name,phone from addrbook";
    $result=$conn->query($sql);

    if($result->num_rows>0){
    while($row=$result->fetch_assoc()){
        echo " name : ". $row["name"]."<br>";
        echo " phone : ".$row["phone"]."<br>";
    }
    }
    ?>
```
![hiphp.JPG](pic/mariadb%2Bphp.png)
