# 📝 Linux系統自動化運維第二週筆記20220223
## 📖 Let’s Encrypt 憑證
### 🔖 安裝步驟
```
01 # yum -y install epel-release mod_ssl certbot
```
### 🔖 申請憑證
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


[root@yichien0d /]# cd /var/www/html
[root@yichien0d html]# mkdir .well-known/pki-validation/ -p
[root@yichien0d html]# cd .well-known/pki-validation/
[root@yichien0d pki-validation]# mv /home/user/06848ED219598073AFBCBEE8D69734DF.txt .
[root@yichien0d pki-validation]# ls
06848ED219598073AFBCBEE8D69734DF.txt



🖊️ editor : yi-chien Liu