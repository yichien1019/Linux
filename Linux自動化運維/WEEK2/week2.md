# ð Linuxç³»çµ±èªååéç¶­ç¬¬äºé±ç­è¨20220223
## ð Letâs Encrypt æè­
### ð å®è£æ­¥é©
```
01 # yum -y install epel-release mod_ssl certbot
```
### ð ç³è«æè­
```
02 # certbot certonly --webroot -w /var/www/html -d yichien.dynv6.net --email yichien1019@gmail.com --agree-tos

03 # yum install python-certbot-apache
```

```
<VirtualHost *:80>

        ServerAdmin webmaster@localhost

	ServerName yichien.dynv6.net

	ServerAlias www.yichien.dynv6.net

        DocumentRoot /var/www/html/

        ErrorLog /var/log/httpd/error.log

        CustomLog /var/log/httpd/access.log combined

RewriteEngine on

RewriteCond %{SERVER_NAME} =www.yichien.dynv6.net [OR]

RewriteCond %{SERVER_NAME} =yichien.dynv6.net

RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]

</VirtualHost>
```

```
04 # cd /var/www/html
05 # mkdir .well-known/pki-validation/ -p
06 # cd .well-known/pki-validation/
07 # mv /home/user/06848ED219598073AFBCBEE8D69734DF.txt .
08 # ls
    06848ED219598073AFBCBEE8D69734DF.txt
```


ðï¸ editor : yi-chien Liu