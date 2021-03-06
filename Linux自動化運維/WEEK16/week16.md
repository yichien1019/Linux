# ð Linuxç³»çµ±èªååéç¶­ç¬¬åå­é±ç­è¨20220601
# ð ANSIBLE
## ð USER
### ð æ°å¢ä½¿ç¨è
```
01 # ansible server1 -m user -a "name=kai uid=1100 state=present"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "comment": "", 
        "create_home": true, 
        "group": 1100, 
        "home": "/home/kai", 
        "name": "kai", 
        "shell": "/bin/bash", 
        "state": "present", 
        "system": false, 
        "uid": 1100
    }
02 # ansible server1 -m command -a "id kai"
    192.168.56.109 | CHANGED | rc=0 >>
    uid=1100(kai) gid=1100(kai) groups=1100(kai)
```
### ð åªé¤ä½¿ç¨è
```
03 # ansible server1 -m user -a "name=kai state=absent"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "force": false, 
        "name": "kai", 
        "remove": false, 
        "state": "absent"
    }
04 # ansible server1 -m command -a "id kai"
    192.168.56.109 | FAILED | rc=1 >>
    id: kai: no such usernon-zero return code
```
### ð åµå»ºç¾¤çµ
```
05 # ansible server1 -m group -a "name=manager state=present"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "gid": 1003, 
        "name": "manager", 
        "state": "present", 
        "system": false
    }
06 # ansible server1 -m user -a "name=kai uid=1100 group=manager state=present"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "comment": "", 
        "create_home": true, 
        "group": 1003, 
        "home": "/home/kai", 
        "name": "kai", 
        "shell": "/bin/bash", 
        "state": "present", 
        "stderr": "useradd: warning: the home directory already exists.\nNot copying any file from skel directory into it.\nCreating mailbox file: File exists\n", 
        "stderr_lines": [
            "useradd: warning: the home directory already exists.", 
            "Not copying any file from skel directory into it.", 
            "Creating mailbox file: File exists"
        ], 
        "system": false, 
        "uid": 1100
    }
07 # ansible server1 -m command -a "id kai"
    192.168.56.109 | CHANGED | rc=0 >>
    uid=1100(kai) gid=1003(manager) groups=1003(manager)
```

# ð PLAYBOOK
### ð å®è£ httpd
```
08 # gedit httpd.conf
    Listen 8080
09 # gedit playbook.yml 
- hosts: server1
    tasks:
    - name: install httpd server
      yum: name=httpd state=present

    - name: start httpd server
      service: name=httpd state=started enabled=yes
10 # ansible-playbook playbook.yml 

    PLAY [server1] *******************************************************************************************

    TASK [Gathering Facts] ***********************************************************************************
    ok: [192.168.56.109]

    TASK [install httpd server] ******************************************************************************
    changed: [192.168.56.109]

    TASK [start httpd server] ********************************************************************************
    changed: [192.168.56.109]

    PLAY RECAP ***********************************************************************************************
    192.168.56.109             : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### ð ä¿®æ¹ HTTPD ç PORT 
```
11 # gedit httpd.conf
    Listen 8081
12 # gedit playbook.yml 
    - hosts: server1
      tasks:
        - name: install httpd server
          yum: name=httpd state=present

        - name: configure httpd server
          copy: src=./httpd.conf dest=/etc/httpd/conf/httpd.conf     //è¤è£½éç½®æª

        - name: start httpd server
          service: name=httpd state=restarted enabled=yes     //è¦restarted ï¼æ´æ¹ port ææçæ (ææ¯ reloaded)
13 # ansible-playbook playbook.yml 

    PLAY [server1] *********************************************************************

    TASK [Gathering Facts] *************************************************************
    ok: [192.168.56.109]

    TASK [install httpd server] ********************************************************
    ok: [192.168.56.109]

    TASK [configure httpd server] ******************************************************
    changed: [192.168.56.109]

    TASK [start httpd server] **********************************************************
    ok: [192.168.56.109]

    PLAY RECAP *************************************************************************
    192.168.56.109             : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### ð ç¶æä½äºæ­¤ä»»åæå·è¡Notifyçé¨å (`reload httpd server`)
```
11 # gedit httpd.conf
    Listen 8082
12 # gedit playbook.yml 
    - hosts: server1
    tasks:
        - name: install httpd server
          yum: name=httpd state=present

        - name: configure httpd server
          copy: src=./httpd.conf dest=/etc/httpd/conf/httpd.conf
          notify: reload httpd server

        - name: start httpd server
          service: name=httpd state=started enabled=yes

    handlers:
        - name: reload httpd server
          service: name=httpd state=reloaded
13 # ansible-playbook playbook.yml 

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install httpd server] ****************************************************************************
    ok: [192.168.56.109]

    TASK [configure httpd server] **************************************************************************
    changed: [192.168.56.109]

    TASK [start httpd server] ******************************************************************************
    ok: [192.168.56.109]

    RUNNING HANDLER [reload httpd server] ******************************************************************
    changed: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### ð app
```
14 # gedit playbookapp.yml 
    - hosts: server1
      vars:
        - app1: httpd
        - app2: vsftpd
      tasks:
        - name: install {{ app1 }} and {{ app2 }}
          yum:
            name:
              - "{{ app1 }}"
              - "{{ app2 }}"
            state: present
15 # ansible-playbook playbookapp.yml 

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install httpd and vsftpd] ************************************************************************
    ok: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
## ð PLAYBOOK å®è£ Vsftpd
```
16 # cat vsftpd.conf 
    anonymous_enable=No
    local_enable=YES
    write_enable=YES
    local_umask=022
    dirmessage_enable=YES
    xferlog_enable=YES
    connect_from_port_20=YES
    xferlog_std_format=YES
    listen=NO
    listen_ipv6=YES
    pam_service_name=vsftpd
    userlist_enable=YES
    tcp_wrappers=YES
17 # gedit playbook.yml
    - hosts: server1
      tasks:
        - name: install vsftpd server
          yum: name=vsftpd state=present

        - name: configure vsftpd
          template: src=./vsftpd.conf dest=/etc/vsftpd/vsftpd.conf
          notify: reload vsftpd

        - name: start vsftpd server
          service: name=vsftpd state=started enabled=yes

    handlers:
        - name: reload vsftpd
          service: name=vsftpd state=reloaded
18 # ansible-playbook playbook.yml 

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install vsftpd server] ***************************************************************************
    ok: [192.168.56.109]

    TASK [configure vsftpd] ********************************************************************************
    ok: [192.168.56.109]

    TASK [start vsftpd server] *****************************************************************************
    changed: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
### ð æ°å¢ä½¿ç¨è & è¨­å®å¯ç¢¼
```
19 # gedit playbook.yml
    - hosts: server1
      tasks:
        - name: install vsftpd server
          yum: name=vsftpd state=present

        - name: add a new user "mary"
          user:
            name: mary
            password: "{{'mary'|password_hash('sha512')}}"

        - name: configure vsftpd
          copy: src=./vsftpd.conf dest=/etc/vsftpd/vsftpd.conf
          notify: reload vsftpd

        - name: start vsftpd server
          service: name=vsftpd state=started enabled=yes

    handlers:
        - name: reload vsftpd
          service: name=vsftpd state=reloaded
20 # ansible-playbook playbook.yml 

    PLAY [server1] *****************************************************************************************

    TASK [Gathering Facts] *********************************************************************************
    ok: [192.168.56.109]

    TASK [install vsftpd server] ***************************************************************************
    ok: [192.168.56.109]

    TASK [add a new user "mary"] ***************************************************************************
    changed: [192.168.56.109]

    TASK [configure vsftpd] ********************************************************************************
    ok: [192.168.56.109]

    TASK [start vsftpd server] *****************************************************************************
    ok: [192.168.56.109]

    PLAY RECAP *********************************************************************************************
    192.168.56.109             : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```


## ð è£åè³æ
* [ç¾ä»£ IT äººä¸å®è¦ç¥éç Ansible èªååçµææå·§](https://chusiang.gitbooks.io/automate-with-ansible/content/15.how-to-use-handlers-in-playbooks.html)



ðï¸ editor : yi-chien Liu
