# 📝 Linux系統自動化運維第十七週筆記20220615
## 📖 ANSIBLE
## 🔖 PLAYBOOK
### 👉 使用 YUM 模組進行安裝 & 額外定義
```
01 # gedit playbook.yml 
    - hosts: server1
      vars_files: ./vars_public.yml

      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
02 # gedit vars_public.yml 
    app1: wget
    app2: gedit
03 # ansible-playbook playbook.yml

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install wget and gedit] **************************************************************************
    ok: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
### 👉 分開來客製化定義
```
04 # gedit playbook.yml 
    - hosts: server1
      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
    - hosts: server2
      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
05 # tree
    .
    ├── group_vars
    │   ├── server1
    │   └── server2
    ├── host_vars
    └── playbook.yml

    2 directories, 3 files
06 # gedit server1    &    server2 
    app1: httpd
    app2: vsftpd
07 # ansible-playbook playbook.yml

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install httpd and vsftpd] ************************************************************************
    ok: [192.168.56.109]

    PLAY [server2] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.110]

    TASK [install httpd and vsftpd] ************************************************************************
    ok: [192.168.56.110]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    192.168.56.110             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### 👉 同一群組不同主機的特別設定
```
08 # gedit playbook.yml 
    - hosts: 192.168.56.109
      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present

    - hosts: 192.168.56.110
      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
09 # tree
    .
    ├── group_vars
    │   └── servers
    ├── host_vars
    │   ├── 192.168.56.109
    │   └── 192.168.56.110
    └── playbook.yml

    2 directories, 4 files
10 # cat servers 
    app1: wget
    app2: vim
11 # ansible-playbook playbook.yml

    PLAY [192.168.56.109] **********************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install wget and vim] ****************************************************************************
    ok: [192.168.56.109]

    PLAY [192.168.56.110] **********************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.110]

    TASK [install wget and vim] ****************************************************************************
    ok: [192.168.56.110]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    192.168.56.110             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### 👉 GROUP & HOST 安裝
```
12 # gedit playbook.yml 
    - hosts: servers
      tasks:
        - name: install {{ app }} 
          yum:
            name:
              - "{{ app }}"
            state: present

    - hosts: 192.168.56.109
      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present

    - hosts: 192.168.56.110
      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
13 # cat group_vars/servers 
    app: vsftpd
14 # cat host_vars/192.168.56.109
    app1: wget
    app2: vim
15 # cat host_vars/192.168.56.110
    app1: httpd
    app2: curl
16 # tree
    .
    ├── group_vars
    │   └── servers
    ├── host_vars
    │   ├── 192.168.56.109
    │   └── 192.168.56.110
    └── playbook.yml

    2 directories, 4 files
17 # ansible-playbook playbook.yml 

    PLAY [servers] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]
    ok: [192.168.56.110]

    TASK [install vsftpd] **********************************************************************************
    ok: [192.168.56.109]
    ok: [192.168.56.110]

    PLAY [192.168.56.109] **********************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install wget and vim] ****************************************************************************
    ok: [192.168.56.109]

    PLAY [192.168.56.110] **********************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.110]

    TASK [install httpd and curl] **************************************************************************
    ok: [192.168.56.110]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    192.168.56.110             : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### 👉 在命令列上確認執行機器
```
18 # gedit playbook.yml 
    - hosts: "{{ host }}"
      vars_files: ./vars_public.yml

      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
19 # gedit vars_public.yml 
    app1: wget
    app2: gedit
20 # ansible-playbook -e host=server2 playbook.yml

    PLAY [server2] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.110]

    TASK [install wget and gedit] **************************************************************************
    ok: [192.168.56.110]

    PLAY RECAP *********************************************************************************************
    192.168.56.110             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### 👉 命令列修改優先權
```
21 # gedit playbook.yml 
    - hosts: "{{ host }}"
      vars_files: ./vars_public.yml

      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
22 # gedit vars_public.yml 
    app1: wget
    app2: gedit
23 # ansible-playbook -e host=server1 -e app1=vim playbook.yml 

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install vim and gedit] ***************************************************************************
    ok: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
### 👉 將執行結果存取到變數裡
```
24 # gedit playbook.yml 
    - hosts: server1
      tasks:
        - name: install httpd server
          yum: name=httpd state=present

        - name: configure httpd server
          copy: src=./httpd.conf dest=/etc/httpd/conf/httpd.conf
          notify: restart httpd server

        - name: start httpd server
          service: name=httpd state=started enabled=yes

        - name: Check httpd server
          shell: ps aux|grep httpd
          register: check_httpd     //存到變數裡面
        
        - name: output variable
          debug:
            msg: "{{ check_httpd.stdout_lines }}"

      handlers:
        - name: restart httpd server
          service: name=httpd state=restarted
25 # ansible-playbook playbook.yml

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install httpd server] ****************************************************************************
    ok: [192.168.56.109]

    TASK [configure httpd server] **************************************************************************
    changed: [192.168.56.109]

    TASK [start httpd server] ******************************************************************************
    ok: [192.168.56.109]

    TASK [Check httpd server] ******************************************************************************
    changed: [192.168.56.109]

    TASK [output variable] *********************************************************************************
    ok: [192.168.56.109] => {
        "msg": [
            "root      1081  0.0  0.2 224080  5080 ?        Ss   09:17   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    7647  0.0  0.1 226164  3084 ?        S    10:21   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    7648  0.0  0.1 226164  3084 ?        S    10:21   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    7649  0.0  0.1 226164  3084 ?        S    10:21   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    7650  0.0  0.1 226164  3276 ?        S    10:21   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    7651  0.0  0.1 226164  3288 ?        S    10:21   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "root      8714  0.0  0.0 113284  1208 pts/1    S+   10:37   0:00 /bin/sh -c ps aux|grep httpd", 
            "root      8716  0.0  0.0 112816   956 pts/1    S+   10:37   0:00 grep httpd"
        ]
    }

    RUNNING HANDLER [restart httpd server] *****************************************************************
    changed: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=7    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### 👉 取得特定數值
```
26 # gedit playbook.yml 
    - hosts: server1
      tasks:
        - name: install httpd server
          yum: name=httpd state=present

        - name: configure httpd server
          copy: src=./httpd.conf dest=/etc/httpd/conf/httpd.conf
          notify: restart httpd server

        - name: start httpd server
          service: name=httpd state=started enabled=yes

        - name: Check httpd server
          shell: ps aux|grep httpd
          register: check_httpd
        
        - name: output variable
          debug:
            msg: "{{ check_httpd.stdout_lines }}"

        - name: get the MemTotal
          shell: cat /proc/meminfo | grep MemTotal | awk '{print $2}'
          register: memTotal

        - name: xxxx
          copy: content="{{ memTotal.stdout_lines }}" dest="/tmp/mem.txt"

      handlers:
        - name: restart httpd server
          service: name=httpd state=restarted
27 # ansible-playbook playbook.yml

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install httpd server] ****************************************************************************
    ok: [192.168.56.109]

    TASK [configure httpd server] **************************************************************************
    ok: [192.168.56.109]

    TASK [start httpd server] ******************************************************************************
    ok: [192.168.56.109]

    TASK [Check httpd server] ******************************************************************************
    changed: [192.168.56.109]

    TASK [output variable] *********************************************************************************
    ok: [192.168.56.109] => {
        "msg": [
            "root      8794  0.0  0.2 224080  5036 ?        Ss   10:37   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    8795  0.0  0.1 226164  3092 ?        S    10:37   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    8796  0.0  0.1 226164  3092 ?        S    10:37   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    8797  0.0  0.1 226164  3092 ?        S    10:37   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    8798  0.0  0.1 226164  3092 ?        S    10:37   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "apache    8799  0.0  0.1 226164  3092 ?        S    10:37   0:00 /usr/sbin/httpd -DFOREGROUND", 
            "root      9918  0.0  0.0 113284  1204 pts/1    S+   11:00   0:00 /bin/sh -c ps aux|grep httpd", 
            "root      9920  0.0  0.0 112812   960 pts/1    S+   11:00   0:00 grep httpd"
        ]
    }

    TASK [get the MemTotal] ********************************************************************************
    changed: [192.168.56.109]

    TASK [xxxx] ********************************************************************************************
    changed: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=8    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
28 # ansible server1 -m shell -a "cat /tmp/mem.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    ["1881892"]
```
### 👉 延伸
```
29 # gedit playbook.yml 
    - hosts: servers
      tasks:
        - name: install httpd server
          yum: name=httpd state=present

        - name: configure httpd server
          template: src=./httpd.conf.j2 dest=/etc/httpd/conf/httpd.conf
          notify: restart httpd server

        - name: start httpd server
          service: name=httpd state=started enabled=yes

      handlers:
        - name: restart httpd server
          service: name=httpd state=restarted
30 # ansible-playbook playbook.yml

    PLAY [servers] *****************************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [192.168.56.109]
    ok: [192.168.56.110]

    TASK [install httpd server] ****************************************************
    ok: [192.168.56.109]
    ok: [192.168.56.110]

    TASK [configure httpd server] **************************************************
    changed: [192.168.56.109]
    changed: [192.168.56.110]

    TASK [start httpd server] ******************************************************
    ok: [192.168.56.109]
    ok: [192.168.56.110]

    RUNNING HANDLER [restart httpd server] *****************************************
    changed: [192.168.56.109]
    changed: [192.168.56.110]

    PLAY RECAP *********************************************************************
    192.168.56.109             : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    192.168.56.110             : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
### 👉 快速存取
```
31 # cat memcached
    PORT="11211"
    USER="memcached"
    MAXCONN="1024"
    CACHESIZE="64"
    OPTIONS=""
32 # cat memcached.j2 
    PORT="11211"
    USER="memcached"
    MAXCONN="1024"
    CACHESIZE="{{ ansible_memtotal_mb //2 }}"
    OPTIONS=""
33 # ansible-playbook playbook.yml 

    PLAY [server1] *****************************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [192.168.56.109]

    TASK [install memcached server] ************************************************
    changed: [192.168.56.109]

    TASK [configure memcached server] **********************************************
    changed: [192.168.56.109]

    TASK [service memcached server] ************************************************
    changed: [192.168.56.109]

    TASK [check memcached server] **************************************************
    changed: [192.168.56.109]

    TASK [debug memcached variables] ***********************************************
    ok: [192.168.56.109] => {
        "msg": [
            "memcach+  6693  0.0  0.0 344100  1688 ?        Ssl  16:30   0:00 /usr/bin/memcached -u memcached -p 11211 -m 918 -c 1024", 
            "root      6767  0.0  0.0 113284  1204 pts/1    S+   16:30   0:00 /bin/sh -c ps aux | grep memcached", 
            "root      6769  0.0  0.0 112812   960 pts/1    S+   16:30   0:00 grep memcached"
        ]
    }

    PLAY RECAP *********************************************************************
    192.168.56.109             : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### 👉 更改 PORT & 對應配置檔
```
34 # vim /etc/ssh/sshd_config 
    Port 220
35 # systemctl restart sshd
36 # ansible server1 -m ping
    192.168.56.109 | UNREACHABLE! => {
        "changed": false, 
        "msg": "Failed to connect to the host via ssh: ssh: connect to host 192.168.56.109 port 22: Connection refused", 
        "unreachable": true
    }
37 # gedit /etc/ansible/hosts
    [server1]
    192.168.56.109  ansible_ssh_port=220
    [server2]
    192.168.56.110  
    [servers]
    192.168.56.109
    192.168.56.110
38 # ansible server1 -m ping
    192.168.56.109 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "ping": "pong"
    }
```

🖊️ editor : yi-chien Liu

