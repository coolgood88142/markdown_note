---
title: "RequestCookieJar與LWPCookieJar兩種流程"
date: "2019-11-25"
author: "RequestCookieJar"
summary: "說明RequestCookieJar與LWPCookieJar兩種流程"
---



## 1.RequestCookieJar

`RequestCookieJar`是`python`的`requests`套件的library，在請求任何網址時，會從中拿到瀏覽器的`cookie`記錄，在系統登入頁面輸入完帳號密碼，執行登入成功後，就會拿到`requests`套件的`cookie`。



## 2.LWPCookieJar

用`python`的`http`套件`cookiejar`的`library`，其中FileCookieJar的類別，主要將RequestCookieJar物件的瀏覽器的`cookie`記錄，用檔案的方式做保存，將Set-Cookie在瀏覽器的描述內容，儲存到檔案裡。

補上另一種FileCookieJar的說明



3.SimpleCookieJar



這幾個用法與範例要寫出來(總共4個)



## 循序圖

![CookieJarUML](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/CookieJarUML.png)

將cookie資料傳到首頁改成傳到瀏覽器上



## 流程圖

![sport-cookie](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/sport-cookie.png)

參考資料:



