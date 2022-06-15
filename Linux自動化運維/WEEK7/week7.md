# 📝 Linux系統自動化運維第七週筆記20220330
# 📖 JUMPSERVER
### 👉 安裝 DOCKER-COMPOSE
```
01 # sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
02 # sudo chmod +x /usr/local/bin/docker-compose
03 # docker-compose --version
    docker-compose version 1.29.2, build 5becea4c
```
### 👉 部署 DOCKER-COMPOSE
```
04 # systemctl start docker
05 # git clone --depth=1 https://github.com/wojiushixiaobai/Dockerfile.git
06 # cd Dockerfile
07 # cp config_example.conf .env
08 # gedit .env
    DOCKER_SUBNET=172.20.0.0/16
09 # docker-compose -f docker-compose-redis.yml -f docker-compose-mariadb.yml -f docker-compose.yml up
```
#### 📍 失敗欲執行指令
```
10 # docker rm -f $(docker ps -a -q)
11 # docker-compose down
    Removing network jms_net
```
* 成功
![](PIC/jumpserver_run.png)
## 🔖 JUMPSERVER 設定
### 👉 連線 JUMPSERVER
* 開啟瀏覽器輸入伺服器 IP `192.168.56.104`
![](pic/jumpserver.png)
### 👉 第一步 : 創建管理用戶
* 用戶列表
![](pic/create_user1.png)
![](pic/create_user2.png)
![](pic/create_user3.png)
### 👉 第二步 : 創建系統用戶 ( user & root )
#### 📍 USER
* 系統用戶 - 普通用戶
![](pic/create_system_user1.png)
![](pic/create_system_user2.png)
![](pic/create_system_user3.png)
#### 📍 ROOT
* 系統用戶 - 特權用戶
![](pic/create_system_root1.png)
![](pic/create_system_root2.png)
![](pic/create_system_root3.png)
### 👉 第三步 : 創建資產
* 資產列表
![](pic/create_asset1.png)
![](pic/create_asset2.png)
![](pic/create_asset3.png)
### 👉 第四步 : 創建資產授權
* 資產授權
![](pic/asset_permissions1.png)
![](pic/asset_permissions2.png)
![](pic/asset_permissions3.png)
![](pic/asset_permissions4.png)
### 👉 第五步 : 創建命令過濾器
* 命令過濾
![](pic/command1.png)
### 👉 第六步 : 新增命令過濾器規則
* 命令過濾器規則
![](pic/command_rule1.png)
![](pic/command_rule2.png)
## 🔖 PUTTY 連線
* 輸入 IP `192.168.56.104:2222`
* 輸入 `tom : 123456`
![](pic/jumpserver_connect.png)
![](pic/jumpserver_command.png)


🖊️ editor : yi-chien Liu