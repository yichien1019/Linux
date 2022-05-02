# 📝 SSH 免密碼登入
### 👉 第一步 : 產生 SSH 登入用的金鑰
```markdown
01 # mkdir -p ~/.ssh
02 # chmod 700 ~/.ssh
03 # ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/root/.ssh/id_rsa): 
    /root/.ssh/id_rsa already exists.
    Overwrite (y/n)? y
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /root/.ssh/id_rsa.
    Your public key has been saved in /root/.ssh/id_rsa.pub.
    The key fingerprint is:
    SHA256:Do8HcA2MlRO908VAPD14WeNcDZvPy00+sslmTkjBDD8 root@yichien7-1
    The key's randomart image is:
    +---[RSA 2048]----+
    |     +++ +o= o+.o|
    |    . +o. O Bo =.|
    |    . ...o E .=  |
    |     o  o . o  o |
    |      o S. .    +|
    |       *  . . .oo|
    |      . +  . o +o|
    |       .    oo+ .|
    |            +=   |
    +----[SHA256]-----+
```

* `id_rsa.pub`：公開金鑰（public key），這是可以對外公開的金鑰，之後要將它放在遠端的 Linux 伺服器上作認證使用。
* `id_rsa`：私密金鑰（private key），這是要保護好的金鑰，它等同於你的 Linux 密碼，放在自己的電腦中

### 👉 第二步 : 將產生的 `id_rsa.pub` 公鑰複製到 Linux 伺服器上

```markdown
04 # ssh-copy-id user@192.168.56.103
    /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
    /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
    /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
    user@192.168.56.103's password: 

    Number of key(s) added: 1

    Now try logging into the machine, with:   "ssh 'user@192.168.56.103'"
    and check to make sure that only the key(s) you wanted were added.
```

### 👉 第三步 : 使用 SSH 登入
```
05 # ssh user@192.168.56.103
    Last failed login: Sun Jan  2 12:11:43 CST 2022 from 192.168.56.5 on ssh:notty
    There were 2 failed login attempts since the last successful login.
    Last login: Sun Jan  2 12:09:02 2022 from 192.168.56.5
06 $ exit
    logout
    Connection to 192.168.56.103 closed.
```