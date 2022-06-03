# 📝 Linux系統自動化運維第十五週筆記20220525
# 📖 ANSIBLE
## 🔖 ansible 的3個遠端模組的區別
* command : ansible的預設模組，不指定-m引數的時候，使用的就是command模組 
    > 常見的命令都可以使用，但命令的執行不是通過shell來執行的，所以`< > | and & z`這些操作都不可以，不支援管道，沒法批次執行命令
    ```
    01 # ansible server1 -a "pwd"
    192.168.56.109 | CHANGED | rc=0 >>
    /root
    ```
* shell模組 : 使用shell模組的時候預設是通過/bin/sh來執行的，所以在終端輸入的各種命令都可以使用
* scripts模組 : 使用scripts模組可以在本地寫一個指令碼，在遠端伺服器上執行

## 🔖 ansible 的命令引數
* anisble命令語法 : <br>
`ansible [-i 主機檔案] [-f 批次] [組名] [-m 模組名稱] [-a 模組引數]`

引數 | 功能
----- | -----
-v | 詳細模式，如果執行成功，輸出詳細結果
-i | 指定host檔案路徑，預設在/etc/ansible/hosts
-f | -forks=NUM	NUM預設是整數5，指定fork開啟同步程序的個數
-m | 指定使用的module名稱，預設command模組
-a | 指定模組的引數
-k | 提示輸入SSH密碼，而不是使用基於ssh金鑰認證
-sudo | 指定使用sudo獲取root許可權
-K | 提示輸入sudo密碼
-u | 指定行動端的執行使用者
-C | 測試命令執行會改變什麼內容，不會真正的去執行
ansible-doc | 詳細引數

### 👉 詳細引數 `ansible-doc -s [模組]` 
```
02 # ansible-doc -s shell
    - name: Execute shell commands on targets
    shell:
        chdir:                 # Change into this directory before running the command.
        cmd:                   # The command to run followed by optional arguments.
        creates:               # A filename, when it already exists, this step will *not* be
                                run.
        executable:            # Change the shell used to execute the command. This expects
                                an absolute path to the
                                executable.
        free_form:             # The shell module takes a free form command to run, as a
                                string. There is no actual
                                parameter named 'free form'.
                                See the examples on how to
                                use this module.
        removes:               # A filename, when it does not exist, this step will *not* be
                                run.
        stdin:                 # Set the stdin of the command directly to the specified
                                value.
        stdin_add_newline:     # Whether to append a newline to stdin data.
        warn:                  # Whether to enable task warnings.
```
### 👉 節點新增使用者 & 檢視是否建立成功
```
03 # ansible server1 -m command -a "useradd yc"
    192.168.56.109 | CHANGED | rc=0 >>
04 # ansible server1 -m command -a "id yc"
    192.168.56.109 | CHANGED | rc=0 >>
    uid=1002(yc) gid=1002(yc) groups=1002(yc)
05 # ansible server1 -m command -a "id ccc"
    192.168.56.109 | FAILED | rc=1 >>
    id: ccc: no such usernon-zero return code
```
### 👉 `command` & `shell` 比較
```
06 # ansible servers -m command -a "ifconfig | grep enp0s8"     //無法執行多行
    192.168.56.109 | FAILED | rc=1 >>
    grep: Unknown host
    ifconfig: `--help' gives usage information.non-zero return code
    192.168.56.110 | FAILED | rc=1 >>
    grep: Unknown host
    ifconfig: `--help' gives usage information.non-zero return code
07 # ansible servers -m shell -a "ifconfig | grep enp0s8"
    192.168.56.110 | CHANGED | rc=0 >>
    enp0s8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
    192.168.56.109 | CHANGED | rc=0 >>
    enp0s8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
```
### 👉 指令順序 
> `ansible [-m 模組名][-a 指令][組名]` && `ansible [組名][-m 模組名][-a 指令]`
* 開機時間
```
08 # ansible -m command -a "uptime" "servers"
    192.168.56.110 | CHANGED | rc=0 >>
    20:44:34 up 31 min,  3 users,  load average: 0.07, 0.04, 0.05
    192.168.56.109 | CHANGED | rc=0 >>
    20:44:35 up 42 min,  3 users,  load average: 0.00, 0.01, 0.05
09 # ansible servers -m command -a "uptime"
    192.168.56.110 | CHANGED | rc=0 >>
    20:44:45 up 32 min,  3 users,  load average: 0.06, 0.04, 0.05
    192.168.56.109 | CHANGED | rc=0 >>
    20:44:46 up 42 min,  3 users,  load average: 0.00, 0.01, 0.05
```
* 記憶體使用情況
```
10 # ansible servers -m command -a "free -m"
    192.168.56.109 | CHANGED | rc=0 >>
                total        used        free      shared  buff/cache   available
    Mem:           1837         798         186          17         852         838
    Swap:          2047           0        2047
    192.168.56.110 | CHANGED | rc=0 >>
                total        used        free      shared  buff/cache   available
    Mem:           1837         805         185          16         847         832
    Swap:          2047           0        2047
11 # ansible -m command -a "free -m" "servers"
    192.168.56.109 | CHANGED | rc=0 >>
                total        used        free      shared  buff/cache   available
    Mem:           1837         798         186          17         852         838
    Swap:          2047           0        2047
    192.168.56.110 | CHANGED | rc=0 >>
                total        used        free      shared  buff/cache   available
    Mem:           1837         805         185          16         847         832
    Swap:          2047           0        2047
```
### 👉 預設工作目錄 (`/root`)
```
12 # ansible server1 -m command -a "echo 'tom' > tom.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    tom > tom.txt
13 # ansible server1 -m command -a "pwd"
    192.168.56.109 | CHANGED | rc=0 >>
    /root
14 # ansible server1 -m command -a "cat  tom.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    tom
```
### 👉 在執行指令前先切換到此目錄 `chdir`
```
15 # ansible server1 -m command -a "cd /tmp && ls"
    192.168.56.109 | CHANGED | rc=0 >>

16 # ansible server1 -m command -a "chdir=/tmp ls"
    192.168.56.109 | CHANGED | rc=0 >>
    abc.txt
    ansible_command_payload_HWKx6Q
    ssh-eTnKojA6ILoy
    systemd-private-794b28c6864e4150971315e923f7aef8-bolt.service-RqN5tK
    systemd-private-794b28c6864e4150971315e923f7aef8-colord.service-UnJOn4
    systemd-private-794b28c6864e4150971315e923f7aef8-cups.service-XrOI14
    systemd-private-794b28c6864e4150971315e923f7aef8-fwupd.service-XgNIlw
    systemd-private-794b28c6864e4150971315e923f7aef8-rtkit-daemon.service-aJeCXo
    tracker-extract-files.1000
```
### 👉 如果已存在，不會執行後面指令
```
17 # ansible server1 -m command -a "creates=a ls"
    192.168.56.109 | SUCCESS | rc=0 >>
    skipped, since a exists
18 # ansible server1 -m command -a "creates=b ls"
    192.168.56.109 | CHANGED | rc=0 >>
    a
    anaconda-ks.cfg
    a.txt
    initial-setup-ks.cfg
    test-ansible
    test.sh
    tom.txt
```
### 👉 如果已存在，會執行後面指令
```
19 # ansible server1 -m command -a "removes=a ls"
    192.168.56.109 | CHANGED | rc=0 >>
    a
    anaconda-ks.cfg
    a.txt
    initial-setup-ks.cfg
    test-ansible
    test.sh
    tom.txt
20 # ansible server1 -m command -a "removes=b ls"
    192.168.56.109 | SUCCESS | rc=0 >>
    skipped, since b does not exist
```
### 👉 拷貝檔案到遠端資料夾
* `COPY` 指令
```
21 # ansible server1 -m copy -a "src=./hello.txt dest=/tmp/hello.txt owner=user group=user mode=0644"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "checksum": "f572d396fae9206628714fb2ce00f72e94f2258f", 
        "dest": "/tmp/hello.txt", 
        "gid": 1000, 
        "group": "user", 
        "md5sum": "b1946ac92492d2347c6235b4d2611184", 
        "mode": "0644", 
        "owner": "user", 
        "size": 6, 
        "src": "/root/.ansible/tmp/ansible-tmp-1653446328.47-20250-166623639454261/source", 
        "state": "file", 
        "uid": 1000
    }
22 # ansible server1 -m command -a "ls -l /tmp/hello.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    -rw-r--r-- 1 user user 6 May 25 10:38 /tmp/hello.txt
23 # ansible server1 -m copy -a "content='hello world' dest=/tmp/hi.txt"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "checksum": "2aae6c35c94fcfb415dbe95f408b9ce91ee846ed", 
        "dest": "/tmp/hi.txt", 
        "gid": 0, 
        "group": "root", 
        "md5sum": "5eb63bbbe01eeed093cb22bb8f5acdc3", 
        "mode": "0644", 
        "owner": "root", 
        "size": 11, 
        "src": "/root/.ansible/tmp/ansible-tmp-1654153148.61-17354-143470001626512/source", 
        "state": "file", 
        "uid": 0
    }
24 # ansible server1 -m command -a "cat /tmp/hi.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    hello world
```
* `FILE`  指令
```
25 # ansible server1 -m file -a "path=/tmp/abc.txt owner=yc group=yc mode=0755"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "gid": 1002, 
        "group": "yc", 
        "mode": "0755", 
        "owner": "yc", 
        "path": "/tmp/abc.txt", 
        "size": 0, 
        "state": "file", 
        "uid": 1002
    }
26 # ansible server1 -m command -a "ls /tmp/abc.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    /tmp/abc.txt

```
### 👉 拷貝檔案到遠端資料夾 & 原資料備份
```
25 # ansible server1 -m copy -a "content='hello world12345' dest=/tmp/hi.txt backup=yes" 
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "backup_file": "/tmp/hi.txt.25945.2022-06-02@15:03:50~", 
        "changed": true, 
        "checksum": "cdabccc6b72f4e9cb9d72ded8c2dccfdabe0f565", 
        "dest": "/tmp/hi.txt", 
        "gid": 0, 
        "group": "root", 
        "md5sum": "aa43927247ca597f8e4fe4629ca3571e", 
        "mode": "0644", 
        "owner": "root", 
        "size": 16, 
        "src": "/root/.ansible/tmp/ansible-tmp-1654153429.43-17922-46416249577129/source", 
        "state": "file", 
        "uid": 0
    }
26 # ansible server1 -m command -a "ls /tmp"
    192.168.56.109 | CHANGED | rc=0 >>
    abc.txt
    ansible_command_payload_dA0G9m
    ansible_yum_payload_ICpVzZ
    hello.txt
    hi.txt
    hi.txt.25945.2022-06-02@15:03:50~     //備份後的檔案
27 # ansible server1 -m command -a "cat /tmp/hi.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    hello world12345 
```
### 👉 刪除檔案
```
28 # ansible server1 -m file -a "path=/tmp/hello.txt state=absent"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "path": "/tmp/hello.txt", 
        "state": "absent"
    }
```
### 👉 創建遠端資料夾
* `directory` 指令
```
29 # ansible server1 -m file -a "path=/tmp/testdir state=directory"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "gid": 0, 
        "group": "root", 
        "mode": "0755", 
        "owner": "root", 
        "path": "/tmp/testdir", 
        "size": 6, 
        "state": "directory", 
        "uid": 0
    }
30 # ansible server1 -m command -a "ls -l /tmp"
    192.168.56.109 | CHANGED | rc=0 >>
    drwxr-xr-x  2 root root      6 Jun  3 15:53 testdir
```
* `TOUCH` 指令
```
31 # ansible server1 -m file -a "path=/tmp/abcd.txt state=touch"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "dest": "/tmp/abcd.txt", 
        "gid": 0, 
        "group": "root", 
        "mode": "0644", 
        "owner": "root", 
        "size": 0, 
        "state": "file", 
        "uid": 0
    }
32 # ansible server1 -m command -a "ls -l /tmp/abcd.txt"
    192.168.56.109 | CHANGED | rc=0 >>
    -rw-r--r-- 1 root root 0 Jun  3 16:19 /tmp/abcd.txt
```

### 👉 獲取遠端檔案資訊 `stat`
```
33 # stat test.sh 
    File: ‘test.sh’
    Size: 50        	Blocks: 8          IO Block: 4096   regular file
    Device: fd00h/64768d	Inode: 69235132    Links: 1
    Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2022-05-29 18:23:34.223926143 +0800
    Modify: 2022-05-25 10:29:16.227044494 +0800
    Change: 2022-05-29 18:23:18.270926143 +0800
    Birth: -
34 # ansible server1 -m stat -a "path=test.sh"
    192.168.56.109 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": false, 
        "stat": {
            "atime": 1654255344.2509742, 
            "attr_flags": "", 
            "attributes": [], 
            "block_size": 4096, 
            "blocks": 8, 
            "charset": "us-ascii", 
            "checksum": "f5436ed502265e0e229508a6ee295d7a5438a025", 
            "ctime": 1653444310.4034436, 
            "dev": 64768, 
            "device_type": 0, 
            "executable": true, 
            "exists": true, 
            "gid": 0, 
            "gr_name": "root", 
            "inode": 67275433, 
            "isblk": false, 
            "ischr": false, 
            "isdir": false, 
            "isfifo": false, 
            "isgid": false, 
            "islnk": false, 
            "isreg": true, 
            "issock": false, 
            "isuid": false, 
            "mimetype": "text/x-shellscript", 
            "mode": "0755", 
            "mtime": 1653444299.132811, 
            "nlink": 1, 
            "path": "test.sh", 
            "pw_name": "root", 
            "readable": true, 
            "rgrp": true, 
            "roth": true, 
            "rusr": true, 
            "size": 49, 
            "uid": 0, 
            "version": "18446744071624643996", 
            "wgrp": false, 
            "woth": false, 
            "writeable": true, 
            "wusr": true, 
            "xgrp": true, 
            "xoth": true, 
            "xusr": true
        }
    }
```
### 👉 遠端安裝軟體
```
35 # ansible server1 -m yum -a "name=httpd state=latest"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "changes": {
            "installed": [
                "httpd"
            ], 
            "updated": []
        }, 
        "msg": "", 
        "rc": 0, 
        "results": [
            ...
        ]
    }
36 # ansible server1 -m shell -a "rpm -qa | grep httpd"
    192.168.56.109 | CHANGED | rc=0 >>
    httpd-tools-2.4.6-97.el7.centos.5.x86_64
    httpd-2.4.6-97.el7.centos.5.x86_64
```
### 👉 遠端卸載軟體
```
36 # ansible server1 -m yum -a "name=httpd state=absent"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "changes": {
            "removed": [
                "httpd"
            ]
        }, 
        "msg": "", 
        "rc": 0, 
        "results": [
            ...
        ]
    }
```
### 👉 遠端開啟軟體
```
37 # ansible server1 -m service -a "name=httpd state=started"
    192.168.56.109 | CHANGED => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        }, 
        "changed": true, 
        "name": "httpd", 
        "state": "started", 
        "status": {
            "ActiveEnterTimestampMonotonic": "0", 
            "ActiveExitTimestampMonotonic": "0", 
            "ActiveState": "inactive", 
            "After": "systemd-journald.socket network.target -.mount tmp.mount nss-lookup.target system.slice remote-fs.target basic.target", 
            "AllowIsolate": "no", 
            "AmbientCapabilities": "0", 
            "AssertResult": "no", 
            "AssertTimestampMonotonic": "0", 
            "Before": "shutdown.target", 
            "BlockIOAccounting": "no", 
            "BlockIOWeight": "18446744073709551615", 
            "CPUAccounting": "no", 
            "CPUQuotaPerSecUSec": "infinity", 
            "CPUSchedulingPolicy": "0", 
            "CPUSchedulingPriority": "0", 
            "CPUSchedulingResetOnFork": "no", 
            "CPUShares": "18446744073709551615", 
            "CanIsolate": "no", 
            "CanReload": "yes", 
            "CanStart": "yes", 
            "CanStop": "yes", 
            "CapabilityBoundingSet": "18446744073709551615", 
            "CollectMode": "inactive", 
            "ConditionResult": "no", 
            "ConditionTimestampMonotonic": "0", 
            "Conflicts": "shutdown.target", 
            "ControlPID": "0", 
            "DefaultDependencies": "yes", 
            "Delegate": "no", 
            "Description": "The Apache HTTP Server", 
            "DevicePolicy": "auto", 
            "Documentation": "man:httpd(8) man:apachectl(8)", 
            "EnvironmentFile": "/etc/sysconfig/httpd (ignore_errors=no)", 
            "ExecMainCode": "0", 
            "ExecMainExitTimestampMonotonic": "0", 
            "ExecMainPID": "0", 
            "ExecMainStartTimestampMonotonic": "0", 
            "ExecMainStatus": "0", 
            "ExecReload": "{ path=/usr/sbin/httpd ; argv[]=/usr/sbin/httpd $OPTIONS -k graceful ; ignore_errors=no ; start_time=[n/a] ; stop_time=[n/a] ; pid=0 ; code=(null) ; status=0/0 }", 
            "ExecStart": "{ path=/usr/sbin/httpd ; argv[]=/usr/sbin/httpd $OPTIONS -DFOREGROUND ; ignore_errors=no ; start_time=[n/a] ; stop_time=[n/a] ; pid=0 ; code=(null) ; status=0/0 }", 
            "ExecStop": "{ path=/bin/kill ; argv[]=/bin/kill -WINCH ${MAINPID} ; ignore_errors=no ; start_time=[n/a] ; stop_time=[n/a] ; pid=0 ; code=(null) ; status=0/0 }", 
            "FailureAction": "none", 
            "FileDescriptorStoreMax": "0", 
            "FragmentPath": "/usr/lib/systemd/system/httpd.service", 
            "GuessMainPID": "yes", 
            "IOScheduling": "0", 
            "Id": "httpd.service", 
            "IgnoreOnIsolate": "no", 
            "IgnoreOnSnapshot": "no", 
            "IgnoreSIGPIPE": "yes", 
            "InactiveEnterTimestampMonotonic": "0", 
            "InactiveExitTimestampMonotonic": "0", 
            "JobTimeoutAction": "none", 
            "JobTimeoutUSec": "0", 
            "KillMode": "control-group", 
            "KillSignal": "18", 
            "LimitAS": "18446744073709551615", 
            "LimitCORE": "18446744073709551615", 
            "LimitCPU": "18446744073709551615", 
            "LimitDATA": "18446744073709551615", 
            "LimitFSIZE": "18446744073709551615", 
            "LimitLOCKS": "18446744073709551615", 
            "LimitMEMLOCK": "65536", 
            "LimitMSGQUEUE": "819200", 
            "LimitNICE": "0", 
            "LimitNOFILE": "4096", 
            "LimitNPROC": "7219", 
            "LimitRSS": "18446744073709551615", 
            "LimitRTPRIO": "0", 
            "LimitRTTIME": "18446744073709551615", 
            "LimitSIGPENDING": "7219", 
            "LimitSTACK": "18446744073709551615", 
            "LoadState": "loaded", 
            "MainPID": "0", 
            "MemoryAccounting": "no", 
            "MemoryCurrent": "18446744073709551615", 
            "MemoryLimit": "18446744073709551615", 
            "MountFlags": "0", 
            "Names": "httpd.service", 
            "NeedDaemonReload": "no", 
            "Nice": "0", 
            "NoNewPrivileges": "no", 
            "NonBlocking": "no", 
            "NotifyAccess": "main", 
            "OOMScoreAdjust": "0", 
            "OnFailureJobMode": "replace", 
            "PermissionsStartOnly": "no", 
            "PrivateDevices": "no", 
            "PrivateNetwork": "no", 
            "PrivateTmp": "yes", 
            "ProtectHome": "no", 
            "ProtectSystem": "no", 
            "RefuseManualStart": "no", 
            "RefuseManualStop": "no", 
            "RemainAfterExit": "no", 
            "Requires": "system.slice -.mount basic.target", 
            "RequiresMountsFor": "/var/tmp", 
            "Restart": "no", 
            "RestartUSec": "100ms", 
            "Result": "success", 
            "RootDirectoryStartOnly": "no", 
            "RuntimeDirectoryMode": "0755", 
            "SameProcessGroup": "no", 
            "SecureBits": "0", 
            "SendSIGHUP": "no", 
            "SendSIGKILL": "yes", 
            "Slice": "system.slice", 
            "StandardError": "inherit", 
            "StandardInput": "null", 
            "StandardOutput": "journal", 
            "StartLimitAction": "none", 
            "StartLimitBurst": "5", 
            "StartLimitInterval": "10000000", 
            "StartupBlockIOWeight": "18446744073709551615", 
            "StartupCPUShares": "18446744073709551615", 
            "StatusErrno": "0", 
            "StopWhenUnneeded": "no", 
            "SubState": "dead", 
            "SyslogLevelPrefix": "yes", 
            "SyslogPriority": "30", 
            "SystemCallErrorNumber": "0", 
            "TTYReset": "no", 
            "TTYVHangup": "no", 
            "TTYVTDisallocate": "no", 
            "TasksAccounting": "no", 
            "TasksCurrent": "18446744073709551615", 
            "TasksMax": "18446744073709551615", 
            "TimeoutStartUSec": "1min 30s", 
            "TimeoutStopUSec": "1min 30s", 
            "TimerSlackNSec": "50000", 
            "Transient": "no", 
            "Type": "notify", 
            "UMask": "0022", 
            "UnitFilePreset": "disabled", 
            "UnitFileState": "disabled", 
            "WatchdogTimestampMonotonic": "0", 
            "WatchdogUSec": "0"
        }
    }
38 # ansible server1 -m command -a "systemctl status httpd"
    192.168.56.109 | CHANGED | rc=0 >>
    ● httpd.service - The Apache HTTP Server
    Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
    Active: active (running) since Fri 2022-06-03 20:00:11 PST; 1min 33s ago
        Docs: man:httpd(8)
            man:apachectl(8)
    Main PID: 15774 (httpd)
    Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
        Tasks: 6
    CGroup: /system.slice/httpd.service
            ├─15774 /usr/sbin/httpd -DFOREGROUND
            ├─15779 /usr/sbin/httpd -DFOREGROUND
            ├─15780 /usr/sbin/httpd -DFOREGROUND
            ├─15781 /usr/sbin/httpd -DFOREGROUND
            ├─15782 /usr/sbin/httpd -DFOREGROUND
            └─15783 /usr/sbin/httpd -DFOREGROUND

    Jun 03 20:00:11 yichien1-2 systemd[1]: Starting The Apache HTTP Server...
    Jun 03 20:00:11 yichien1-2 httpd[15774]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 192.168.56.109. Set the 'ServerName' directive globally to suppress this message
    Jun 03 20:00:11 yichien1-2 systemd[1]: Started The Apache HTTP Server.
```
### 👉 顯示所有資訊
```
39 # ansible server1 -m setup | grep cpu
        "ansible_processor_vcpus": 2, 
40 # ansible server1 -m setup | grep mem
        "ansible_memfree_mb": 187, 
        "ansible_memory_mb": {
        "ansible_memtotal_mb": 1837, 
41 # ansible server2 -m setup | grep cpu
        "ansible_processor_vcpus": 2, 
42 # ansible server2 -m setup | grep mem
        "ansible_memfree_mb": 139, 
        "ansible_memory_mb": {
        "ansible_memtotal_mb": 1837, 
``` 
## 🔖 腳本
* 撰寫腳本
```
43 # gedit test.sh
    #!/usr/bin/bash
    date
    hostname
    echo "hello world"
44 # chmod +x test.sh
45 # ./test.sh      //本地端測試
    Sun May 29 18:23:34 PST 2022
    yichien1-1
    hello world
```
* 執行腳本
```
46 # ansible server1 -m script -a "./test.sh"     //相對位置
    192.168.56.109 | CHANGED => {
        "changed": true, 
        "rc": 0, 
        "stderr": "Shared connection to 192.168.56.109 closed.\r\n", 
        "stderr_lines": [
            "Shared connection to 192.168.56.109 closed."
        ], 
        "stdout": "Sun May 29 18:24:45 PST 2022\r\nyichien1-2\r\nhello world\r\n", 
        "stdout_lines": [
            "Sun May 29 18:24:45 PST 2022", 
            "yichien1-2", 
            "hello world"
        ]
    }
47 # ansible server1 -m script -a "/root/test.sh"     //絕對位置
    192.168.56.109 | CHANGED => {
        "changed": true, 
        "rc": 0, 
        "stderr": "Shared connection to 192.168.56.109 closed.\r\n", 
        "stderr_lines": [
            "Shared connection to 192.168.56.109 closed."
        ], 
        "stdout": "Sun May 29 18:25:52 PST 2022\r\nyichien1-2\r\nhello world\r\n", 
        "stdout_lines": [
            "Sun May 29 18:25:52 PST 2022", 
            "yichien1-2", 
            "hello world"
        ]
    }
```
## 📖 補充資料
* [太厲害了，終於有人能把Ansible講的明明白白了，建議收藏](https://tw511.com/a/01/32123.html)



🖊️ editor : yi-chien Liu