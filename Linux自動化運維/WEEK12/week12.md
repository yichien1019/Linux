# ð Linuxç³»çµ±èªååéç¶­ç¬¬åäºé±ç­è¨20220504
# ð PROMETHEUS
## ð GRAPH (http:// `IP` :9090/graph)
* `up {job = "node_exporter2"}`
![](pic/graph1.png)
* `up {job != "node_exporter2"}`
![](pic/graph2.png)
* `up {instance =~ "192.168.56.*"}`
![](pic/graph3.png)
* `up {instance !~ "192.168.56.*"}`
![](pic/graph4.png)
## ð NODE_LOAD (å¹³åéè¼é)
* `node_load1 {instance="192.168.56.108:9100"} [1m]`
![](pic/node_load1.png)
* `node_load1 {instance="192.168.56.108:9100"} offset 5m`
![](pic/node_load2.png)
* `node_load1 {instance="192.168.56.108:9100"} [1m] offset 5m`
![](pic/node_load3.png)

## ð CPU ä½¿ç¨ç
* increase çæææ¯è¡¨ç¤ºå¢é
1. cpu0 5åéå§èæ¼ç©ºéçæçæéï¼ <br>
`increase(node_cpu_seconds_total{cpu="0",mode="idle"}[5m])` <br>
  (node_cpu_seconds_total æ¯å®èª¿éå¢çï¼éåå¬å¼ççµææ¯ç¶åæé»ç node_cpu_seconds_total æ¸å»5åéä¹åç node_cpu_seconds_totalï¼ä¹å°±æ¯é5åéä¹å§èæ¼idle çæç cpu æé)
![](pic/cpu1.png)
2. cpu0 5åéå§èæ¼ç©ºéçæçæéä½æ¯ï¼ <br>
`increase(node_cpu_seconds_total{cpu="0",mode="idle"}[5m]) / increase(node_cpu_seconds_total{cpu="0"}[5m])` <br>
  (åæ¯å¶å¯¦å°±æ¯5åé=300ç§)
![](pic/cpu2.png)
3. ä¸å°ä¸»æ©ææ cpu 5åéå§èæ¼ç©ºéçæçæéä½æ¯ï¼ç¨ sum() å½æ¸ç´¯å åæ ¸æ¸å¼ï¼ <br>
`sum (increase(node_cpu_seconds_total{mode="idle"}[5m])) / sum (increase(node_cpu_seconds_total{mode="idle"}[5m]))`
![](pic/cpu3.png)
4. å¦æ Prometheus ç£æ§å¤å°ä¸»æ©ï¼è¦æ ¹ææ¯å°ä¸»æ©å sumï¼ <br>
`sum by (instance)(increase(node_cpu_seconds_total{mode="idle"}[5m])) / sum by (instance)(increase(node_cpu_seconds_total[5m]))`
![](pic/cpu4.png)
5. cpu ä½¿ç¨ç :  <br>
`1 - cpu ç©ºé²çï¼100 * (1 - sum by (instance)(increase(node_cpu_seconds_total{mode="idle"}[5m])) / sum by (instance)(increase(node_cpu_seconds_total[5m])))`
![](pic/cpu5.png)
## ð å§å­ä½¿ç¨ç
1. node_memory_MemTotal_bytes - ä¸»æ©ä¸çç¸½å§å­
2. node_memory_MemFree_bytes - ä¸»æ©ä¸çç©ºéå§å­
3. node_memory_Buffers_bytes_bytes - ç·©è¡åç·©å­ä¸­çå§å­
4. node_memory_Cached_bytes_bytes - é é¢ç·©å­ä¸­çå§å­
`(node_memory_MemTotal_bytes-(node_memory_MemFree_bytes+ node_memory_Cached_bytes + node_memory_Buffers_bytes)) / node_memory_MemTotal_bytes * 100`
`(node_filesystem_size_bytes{mountpoint="/boot"} - node_filesystem_free_bytes{mountpoint="/boot"}) / node_filesystem_size_bytes{mountpoint="/boot"} * 100`


# ð GRAFANA
## ð å®è£
```
01 # gedit /etc/yum.repos.d/grafana.repo
    [grafana]
    name = grafana
    baseurl = https://packages.grafana.com/oss/rpm
    repo_gpgcheck = 1
    enabled = 1
    gpgcheck = 1
    gpgkey = http://packages.grafana.com/gpg.key
    sslverify = 1
    sslcacert = /etc/pki/tls/certs/ca-bundle.crt
02 # yum install grafana
03 # systemctl daemon-reload
04 # systemctl start grafana-server
05 # systemctl status grafana-server
    â grafana-server.service - Grafana instance
      Loaded: loaded (/usr/lib/systemd/system/grafana-server.service; disabled; vendor preset: disabled)
      Active: active (running) since Wed 2022-05-04 11:17:14 PST; 3s ago
```
### ð ç¬¬ä¸æ­¥ : éåç¶²é 
* http:// `IP` :3000
![](pic/grafana.png)
### ð ç¬¬äºæ­¥ : æ°å¢ Data Source (prometheus)
* HTTPçURL : http:// `IP` :9090
![](pic/datasource.png)
### ð ç¬¬ä¸æ­¥ : æé GRAFANA å®ç¶²é¸æ DASHBOARD
1. é»é¸ Products -> Dashboards
2. Data Source é¸æ Prometheus
3. é¸æ Node Exporter Full
![](PIC/dashboard1.png)
4. Copy ID æèæ¯ Download JSON
![](pic/dashboard2.png)
### ð ç¬¬åæ­¥ : å¨ `http:// `IP` :3000` IMPORT
1. Create -> Import
2. è²¼ä¸ ID or JSON
3. é»é¸ Import
![](PIC/dashboard3.png)
### ð ç¬¬äºæ­¥ : æ¥ç Dashboards
* å° SERVER & CLIENT éåå³å¯æ¥ç
![](pic/dashboard4.png)

# ð PUSHGATEWAY
```
06 # wget https://github.com/prometheus/pushgateway/releases/download/v1.4.2/pushgateway-1.4.2.linux-amd64.tar.gz
07 # tar xf pushgateway-1.4.2.linux-amd64.tar.gz -C /opt/module
08 # chown -R prometheus:prometheus /opt/module/pushgateway/
09 # mv pushgateway-1.4.2.linux-amd64/ pushgateway
10 # gedit /usr/lib/systemd/system/pushgateway.service
    [Unit]
    Description=pushgateway
    After=network.target

    [Service]
    Type=simple
    ExecStart=/opt/module/pushgateway/pushgateway 
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
11 # gedit /opt/module/prometheus/prometheus.yml 
    static_configs:
        - targets: ["localhost:9090"]

    - job_name: "node_exporter1"
      static_configs:
        - targets: ["192.168.56.108:9100"]

    - job_name: "node_exporter2"
      static_configs:
        - targets: ["192.168.56.109:9100","192.168.56.110:9100"]

    - job_name: 'pushgateway'
      honor_labels: true
      static_configs:
        - targets: ['192.168.56.108:9091']
12 # systemctl daemon-reload
13 # systemctl start pushgateway
14 # systemctl status pushgateway
```
![](pic/pushgateway.png)

## ð è£åè³æ
* [Metricsç±»å](https://yunlzheng.gitbook.io/prometheus-book/parti-prometheus-ji-chu/promql/prometheus-metrics-types)
* [Prometheus æ¥è¯¢è¯­è¨ PromQL ç CPU ä½¿ç¨çè®¡ç®æ¹æ³](https://blog.csdn.net/qq_35753140/article/details/105121525)
* [Prometheus çæ§ CPU,Memory,Disk](https://www.modb.pro/db/43636)
* [å¨ CentOS 7 ä¸å®è£ Grafana 6.0.2](http://max043.blogspot.com/2019/03/centos-7-grafana-602.html)


ðï¸ editor : yi-chien Liu




