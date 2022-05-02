# 📝 Linux系統自動化運維第九週筆記20220413
# 📖 JUMPSERVER + Google SMTP寄信
## 🔖 GMAIL 設定
### 👉 第一步：進入 Google 安全設定頁面 
![](pic/mail1.png)
### 👉 第二步：啟用「兩步驟驗證」 
![](pic/mail2.png)
![](pic/mail3.png)
### 👉 第三步：設定「應用程式密碼」 
![](pic/mail4.png)
* 取得密碼
![](pic/mail5.jpg)
### 👉 第四步：設定Google SMTP寄信 
```
SMTP主機：smtp.gmail.com
SMTP 埠號：465
SMTP 安全模式：SSL/TLS
SMTP 認證：是
SMTP 帳號：[你的gmail帳號]
SMTP 密碼：[google應用程式密碼]
```
## 🔖 JUMPSERVER 設定
### 👉 部署
* 郵件服務器
![](pic/stmp_mail1.png)
* 郵件發送測試
![](pic/stmp_mail2.png)
### 👉 測試結果
* EMAIL
![](PIC/stmp_mail3.png)


## 📖 補充資料
* [如何使用Google SMTP寄信(兩段式驗證+應用程式密碼) - 主機架站寄信教學](https://www.webdesigntooler.com/google-smtp-send-mail)


🖊️ editor : yi-chien Liu