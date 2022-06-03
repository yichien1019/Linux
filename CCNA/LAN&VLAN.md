# 📝 
* WAN 廣域網路
    * 廣域網是連接不同地區局域網或者城域網計算機通信的遠程網，所覆蓋的地區範圍通常很大
* WLAN 無限廣域網路
    * 應用越來越多，當前最廣泛使用的當屬 Wi-Fi
    * 只需要一個路由器，就可以讓所有具有無線功能的設備，組成一個無線局域網，非常方便靈活、成本也比較低
* LAN 區域網路
    * 局域網就是小範圍地區形成的一個區域網絡，它的特點就是分佈地區範圍有較小
    * 是一種私有的網絡，相對其他形式網絡傳輸速度更快，性能更穩定，框架搭建容易
* VLAN 虛擬區域網路
    * 虛擬局域網是一組邏輯上的設備和用戶，這些設備和用戶互不受物理位置的限制，相互之間的通信就好像它們在同一個網段
    VLAN 可以把一個實體 LAN 分割成多個虛擬 LAN 使用，分割出來的 VLAN 是各自獨立的，VLAN 與 VLAN 之間互相無法溝通。VLAN 有兩個制式，就是 ISL (Inter-Switch Link) 和 IEEE 802.1q (下稱 802.1q)。ISL 是 Cisco 專有的，只有 Cisco 設備才能設定，802.1q 是公開的，本篇只集中討論 802.1q

原文網址：https://kknews.cc/code/pva3qbe.html
* VPN 虛擬私有網路
    * 在公用網絡上建立專用私有網絡，然後進行加密通訊
## 📖 
### 🔖 
#### 👉 
##### 📍 

操作工作模式
使用者模式
```
Router >
```
特權執行模式
```
Router > en  //進入
Router # exit   //退出
```
整體組態模式
```
Router # config
Router(config)# 
```
特殊組態模式
* 各種介面都有他特定的操作模式
* EX. Interface
```
Router(config)# int [e0/0]
Router(config-if)# 
```
命令查詢






```
$ en
# config
(config) int e0/0
(config-if) R1(config-if)#ip addr 192.168.1.1 255.255.255.0

R1#show ip interface
Ethernet0/0 is up, line protocol is up
  Internet address is 192.168.1.1/24
  Broadcast address is 255.255.255.255

R1#show ip int brief
Interface              IP-Address      OK? Method Status    
            Protocol
Ethernet0/0            192.168.1.1     YES manual up        
            up      
Ethernet0/1            unassigned      YES unset  administra
tively down down    
Ethernet0/2            unassigned      YES unset  administra
tively down down    
Ethernet0/3            unassigned      YES unset  administra
tively down down  

R1#show ip in brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            192.168.1.1     YES manual up                    up      
Ethernet0/1            unassigned      YES unset  administratively down down    
Ethernet0/2            unassigned      YES unset  administratively down down    
Ethernet0/3            unassigned      YES unset  administratively down down  
```

```
SW1#sh vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- ------------
-------------------
1    default                          active    Et0/0, Et0/1
, Et0/2, Et0/3
10   yellow                           active    
20   green                            active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
```