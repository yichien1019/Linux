# 📝 PPTPD SERVER
## 📖 
### 🔖 
#### 👉 
##### 📍 
## 📖 補充資料
* [[筆記] PPTP VPN 伺服器架設](https://project.zhps.tp.edu.tw/ethan/2010/06/pptp-vpn/)
* [ubuntu linux 連線vpn伺服器(pptpd)](https://www.796t.com/content/1548965365.html)
* [點對點隧道協定](https://zh.wikipedia.org/zh-tw/%E9%BB%9E%E5%B0%8D%E9%BB%9E%E9%9A%A7%E9%81%93%E5%8D%94%E8%AD%B0)
* [[教學][Ubuntu 架站] 在 Ubuntu 18.04 / 19.10 安裝 Apache 網頁伺服器，並架設多個網站（多網域）](https://ui-code.com/archives/179)
🖊️ editor : yi-chien Liu


apache2ctl configtest

如何更改Apache server 的 listen port
1. /etc/apache2/ports.conf 把 Listen 80 改成想要的 port
2. /etc/apache2/sites-enabled/000-default.conf 把 <VirtialHost *:80> 換成想要的port即可
3. sudo systemctl restart apache2



yichien1019@test:/var/www/html$ sudo /etc/init.d/pptpd restart restart
[ ok ] Restarting pptpd (via systemctl): pptpd.service.