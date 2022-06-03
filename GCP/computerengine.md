在 Compute Engine 中建立主從式應用程式

您可以瞭解如何執行下列作業：

設定專案

    請選取現有專案，或是建立新的專案。

    啟用 API
    正在啟用 API
    Compute Engine API

建立伺服器 VM

    在 Compute Engine 頁面上，按一下「VM instances」(VM 執行個體)。

    如要查看相關位置，請點選下列按鈕： Compute Engine  VM 執行個體

    在「VM instances」(VM 執行個體) 頁面上，按一下「Create instance」(建立執行個體)。

    在「Name」(名稱) 欄位中輸入 server。

    在「Region」(區域) 清單中選取「us-central1」。

    在「Zone」(可用區) 清單中選取「us-central1-a」。

    在「Machine type」(機器類型) 清單中選取「e2-micro」。

    在「Boot disk」(開機磁碟) 中按一下「Change」(變更)，然後：

    在「Operating system」(作業系統) 清單中選取「Ubuntu」。
    在「Version」(版本) 清單中選取「Ubuntu 18.04 LTS」。
    按一下「Select」(選取)。
    在「Firewall」(防火牆) 區段中，選取「Allow HTTP traffic」(允許 HTTP 流量)。

    按一下「Create」(建立)。

建立用戶端 VM

    在「VM instances」(VM 執行個體) 頁面上，按一下「Create instance」(建立執行個體)。

    在「Name」(名稱) 欄位中輸入 client。

    在「Region」(區域) 清單中選取「us-central1」。

    在「Zone」(可用區) 清單中選取「us-central1-a」。

    在「Machine type」(機器類型) 清單中選取「e2-micro」。

    在「Boot disk」(開機磁碟) 中按一下「Change」(變更)，然後：

    在「Operating system」(作業系統) 清單中選取「Ubuntu」。
    在「Version」(版本) 清單中選取「Ubuntu 18.04 LTS」。
    按一下「Select」(選取)。
    在「Firewall」(防火牆) 區段中，選取「Allow HTTP traffic」(允許 HTTP 流量)。

    按一下「Create」(建立)。

    等待 VM 建立程序完成。按一下「Notifications」(通知) 按鈕即可查看進度。

在伺服器 VM 上安裝及執行資料庫

    按一下「Activate Cloud Shell」(啟用 Cloud Shell) 按鈕。

    使用 SSH 連線至伺服器 VM：
    gcloud compute --project "crafty-coral-350106" ssh --zone us-central1-a server
    
    更新套件並安裝資料庫：
    sudo apt-get update
    sudo apt-get install -y mongodb

    停止資料庫，然後將其重新設定為在特定資料夾中執行：
    sudo service mongodb stop
    sudo mkdir $HOME/db
    sudo mongod --dbpath $HOME/db \
        --port 80 --fork --logpath \
        /var/tmp/mongodb --bind_ip_all

    結束 SSH 工作階段，並中斷與伺服器 VM 的連線：
    exit

在用戶端 VM 上安裝應用程式

    使用 SSH 連線至用戶端 VM：
    gcloud compute --project "crafty-coral-350106" ssh --zone us-central1-a client

    安裝依附元件：
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install git nodejs -y

    複製範例應用程式：
    git clone https://github.com/GoogleCloudPlatform/todomvc-mongodb.git

    安裝應用程式依附元件：
    cd todomvc-mongodb
    sudo npm install -g npm-check-updates
    sudo ncu -u
    sudo npm install

    將程式碼指向正確的通訊埠：
    sed -i -e 's/8080/80/g' server.js

在用戶端 VM 上啟動應用程式。





## 📖 補充資料
* [[手把手教學] 為 Google Cloud VM (GCE) 設定靜態 IP 位址](https://ikala.cloud/gce-static-ip-address/)
* [第一次在 GCP 開 VM 就成功](https://tech.ray247k.com/blog/202201-gcp-vm-basic/)


🖊️ editor : yi-chien Liu




