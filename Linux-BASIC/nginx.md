# NGINX
## 安裝
### 設定 `.repo` 檔
```
01 # vim /etc/yum.repos.d/nginx.repo
    [nginx-stable]
    name=nginx stable repo
    baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
    gpgkey=https://nginx.org/keys/nginx_signing.key
    gpgcheck=1
    enabled=1
```
### yum 安裝
```
02 # yum install nginx
```
### 連線 
![](pic/nginx_setting.png)

## 設定檔
* 設定檔主檔 `/etc/nginx/nginx.conf`
```
# Nginx 的啟用的 Linux 帳戶
user  nginx;

# Nginx 的執行緒數量
worker_processes  auto;

# Error Log 檔的位置
error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    # 允許同一時間連線總數量
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    # 預設的 log 記錄格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    # Access log 檔的位置
    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    # 預設不會自動啟動 gzip 壓縮
    #gzip  on;

    # 載入 /etc/nginx/conf.d/ 下的所有設定檔
    include /etc/nginx/conf.d/*.conf;
}
```
* 預設主機的配置 `/etc/nginx/conf.d/default.conf`
```
server {
    # 預設監聽的port為80
    listen       80;
    server_name  localhost;

    # 可修改 log 的存放位置
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    # 發生 404 導入的錯誤頁面檔名
    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```



# 目錄
* `/var/www/html`：實際的 Web 內容（默認情況下僅包含您之前看到的默認 Nginx 頁面）是從/var/www/html目錄中提供的。這可以通過更改Nginx配置文件來更改。
* `/etc/nginx`：Nginx 配置目錄。所有 Nginx 配置文件都放在這底下。
* `/etc/nginx/nginx.conf`：主要的 Nginx 配置文件。可以對此修改 Nginx 全局配置。
* `/etc/nginx/sites-available/`：可以存儲每站點服務器塊的目錄。
除非鏈接到此目錄，否則 Nginx 不會使用此目錄中的配置文件 sites-enabled。
通常，所有服務器塊配置都在此目錄中完成，然後通過鏈接到其他目錄來啟用。
* `/etc/nginx/sites-enabled/`：存儲已啟用的每站點服務器塊的目錄。
是通過鏈接到 sites-available 目錄的配置文件來創建的。
* `/var/log/nginx/access.log`：Web 服務器的日誌文件。
* `/var/log/nginx/error.log`：任何 Nginx 錯誤都記錄在此日誌中。