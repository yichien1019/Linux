# π Linuxη³»η΅±θͺεειηΆ­η¬¬δΈι±η­θ¨20220302
# π Network Namespace
* ζδ½ε­ε½δ»€ `ip netns`
## π ε©ε namespace ιηιδΏ‘
![](pic/namespace2.png)
### π ε΅ε»Ί namespace
```
01 # ip netns add net1
02 # ip netns add net0
03 # ip netns ls
    net0
    net1
```
### π ε΅ε»Ί veth pair
```
04 # ip link add type veth
05 # ifconfig -a
    veth0: flags=4098<BROADCAST,MULTICAST>  mtu 1500
            ether da:f0:ba:82:7d:c9  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    veth1: flags=4098<BROADCAST,MULTICAST>  mtu 1500
            ether 1a:9b:9e:14:c7:b2  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
### π ε° veth ε ε₯ε° namespace δΈ­
```
06 # ip link set dev veth0 netns net0
07 # ip link set dev veth1 netns net1
08 # ip netns exec net0 ifconfig -a
    lo: flags=8<LOOPBACK>  mtu 65536
            loop  txqueuelen 1000  (Local Loopback)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    veth0: flags=4098<BROADCAST,MULTICAST>  mtu 1500
            ether da:f0:ba:82:7d:c9  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
### π η΅¦ veth pair ιδΈ ip ε°ε
```
09 # ip netns exec net0 ip link set dev veth0 name eth0
10 # ip netns exec net0 ip addr add 10.0.1.1/24 brd + dev eth0
11 # ip netns exec net0 ifconfig eth0 up
12 # ip netns exec net1 ip link set dev veth1 name eth0
13 # ip netns exec net1 ip addr add 10.0.1.2/24 brd + dev eth0
14 # ip netns exec net1 ifconfig eth0 up
```
### π η¨ ping ζΈ¬θ©¦ι£ιζ§
#### π Centos01 
```
15 # ip netns exec net0 ping 10.0.1.2
        PING 10.0.1.2 (10.0.1.2) 56(84) bytes of data.
        64 bytes from 10.0.1.2: icmp_seq=1 ttl=64 time=0.033 ms
        64 bytes from 10.0.1.2: icmp_seq=2 ttl=64 time=0.069 ms
        64 bytes from 10.0.1.2: icmp_seq=3 ttl=64 time=0.087 ms
17 # ip netns exec net0 curl http://10.0.1.2/hi.htm
        hi
```
#### π Centos02
```
16 # ip netns exec net1 echo "hi" > hi.htm
18 # ip netns exec net1 python -m SimpleHTTPServer 80
        Serving HTTP on 0.0.0.0 port 80 ...
        10.0.1.1 - - [01/Mar/2022 20:49:12] "GET /hi.htm HTTP/1.1" 200 -
```
## π ε€εδΈε namespace ιηιδΏ‘
![](pic/namespace3.png)
### π ε΅ε»Ί namespace
```
01 # ip netns add net0
02 # ip netns add net1
03 # ip netns add net2
04 # ip netns ls
    net0
    net1
    net2
```
### π ε΅ε»Ί veth pair
```
05 # ip link add type veth
06 # ip link add type veth
07 # ip link add type veth
```
### π ε° veth ε ε₯ε° namespace δΈ­
```
08 # ip link set veth1 netns net0
09 # ip link set veth3 netns net1
10 # ip link set veth5 netns net2
```
### π ε° veth pair δΈιζε¨ namespace δΈοΌδΈιζε¨bridgeδΈ οΌεθ¨­ipε°ε
```
(1) ιη½?η¬¬δΈε net0
11 # ip netns exec net0 ip link set dev veth1 name eth0
12 # ip netns exec net0 ip addr add 10.0.1.1/24 dev eth0
13 # ip netns exec net0 ip link set dev eth0 up
14 # ip link set dev veth0 up

(2) ιη½?η¬¬δΊε net1
15 # ip netns exec net1 ip link set dev veth3 name eth0
16 # ip netns exec net1 ip addr add 10.0.1.2/24 dev eth0
17 # ip netns exec net1 ip link set dev eth0 up
18 # ip link set dev veth2 up

(3) ιη½?η¬¬δΈε net2
19 # ip netns exec net2 ip link set dev veth5 name eth0
20 # ip netns exec net2 ip addr add 10.0.1.3/24 dev eth0
21 # ip netns exec net2 ip link set dev eth0 up
22 # ip link set dev veth4 up

```
# π Linux Bridge
## π ε?θ£ζ­₯ι©
```
01 # yum install bridge-utils
```
### π ζ°ε»Ί bridge `brctl addbr <bridge>`
```
02 # brctl addbr br0
```
### π ζ°ε’θ£η½? (veth) ε° bridge `brctl addif <bridge> veth`
```
03 # brctl addif br0 veth0
04 # brctl addif br0 veth2
05 # brctl addif br0 veth4
```
### π ι‘―η€ΊηΆε bridge εεΆζι£η·ηηΆ²θ·―ε  `brctl show`
```
06 # brctl show br0
        bridge name	bridge id		STP enabled	interfaces
        br0		8000.56c6b40c49a5	no		veth0
                                                                veth2
                                                                veth4
```
### π εε bridge 
```
07 # ifconfig br0 up
08 # ifconfig veth0 up
09 # ifconfig veth2 up
10 # ifconfig veth4 up
```
### π ζΈ¬θ©¦ηΆ²θ·―ι£ιζ§
```
11 # ip netns exec net0 ping 10.0.1.2
        PING 10.0.1.2 (10.0.1.2) 56(84) bytes of data.
        64 bytes from 10.0.1.2: icmp_seq=1 ttl=64 time=0.073 ms
        64 bytes from 10.0.1.2: icmp_seq=2 ttl=64 time=0.074 ms
        ^C
        --- 10.0.1.2 ping statistics ---
        2 packets transmitted, 2 received, 0% packet loss, time 1000ms
        rtt min/avg/max/mdev = 0.073/0.073/0.074/0.008 ms
12 # ip addr add 10.0.1.254/24 brd + dev br0
```
# π Docker
## π εΊζ¬ζ¦εΏ΅
![](pic/dockercircle.png)
* ζ εζͺοΌImageοΌ
  * Docker ζ εζͺε°±ζ―δΈεε―θ?ηζ¨‘ζΏ
  * ζ εζͺε―δ»₯η¨δΎε»Ίη« Docker ε?Ήε¨
* ε?Ήε¨οΌContainerοΌ
  * ε?Ήε¨ζ―εΎζ εζͺε»Ίη«ηε·θ‘ε―¦δΎ
  * Docker ε©η¨ε?Ήε¨δΎε·θ‘ζη¨
  * ε―δ»₯θ’«εεγιε§γεζ­’γεͺι€
  * ζ―εε?Ήε¨ι½ζ―ηΈδΊιι’ηγδΏθ­ε?ε¨ηεΉ³ε°
* εεΊ«οΌRepositoryοΌ
  * εεΊ«ζ―ιδΈ­ε­ζΎζ εζͺζͺζ‘ηε ΄ζ
  * ζ―εεεΊ«δΈ­εεε«δΊε€εζ εζͺ
  * ζ―εζ εζͺζδΈεηζ¨η±€οΌtagοΌ
  * εεΊ«εηΊε¬ιεεΊ«οΌPublicοΌεη§ζεεΊ«οΌPrivateοΌε©η¨?ε½’εΌ
## π ε?θ£ζ­₯ι©
```
01 # yum install -y yum-utils
02 # sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
03 # sudo yum install docker-ce docker-ce-cli containerd.io
```
## π Image ζ εζͺεΈΈη¨ζδ»€
ζδ»€ | θͺͺζ | η―δΎ
---- | ---- | ----
search | ζε° | docker search centos
pull | δΈθΌ | docker pull centos
images | εθ‘¨ | docker images
run | ε·θ‘ | docker run -it ubuntu echo "hi"
build | ε»Ίη« | docker build -t member:1 .
login | η»ε₯ | docker login docker.okborn.com
push | δΈε³ | 	docker push
### π δ½Ώη¨η―δΎ
```
04 # docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    54c9d81cbb44   4 weeks ago    72.8MB
hello-world   latest    feb5d9fea6a5   5 months ago   13.3kB
05 # docker run -it ubuntu echo "hi"
hi
```
## π θ£εθ³ζ
* [θ―¦θ§£δΊθ?‘η?η½η»εΊε±ζζ―ββLinux network namespace εηδΈε?θ·΅](https://segmentfault.com/a/1190000018391069)


ποΈ editor : yi-chien Liu

