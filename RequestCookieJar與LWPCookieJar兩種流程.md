---
title: "RequestCookieJar與LWPCookieJar兩種流程"
date: "2019-11-25"
author: "RequestCookieJar"
summary: "說明RequestCookieJar與LWPCookieJar兩種流程"
---



## 1.RequestCookieJar

`RequestCookieJar`是`python`的`requests`套件的library，在請求任何網址時，會從中拿到瀏覽器的`cookie`記錄，在系統登入頁面輸入完帳號密碼，執行登入成功後，就會拿到`requests`套件的`cookie`。

```python
impoty requests

#建立requests的session物件
crawler = requests.Session()
url = '登入的網址'

formtable={
    'account':'',
    'password':''
}
formtable['account'] = '帳號'
formtable['password'] = '密碼'

#data就是帶帳號密碼的資料進行登入
crawler.port(url, data = formtable)

#這時session物件的cookie就會產生RequestCookieJar物件
print(crawler.cookies)
#RequestCookieJar[Cookie.ASP.NET_SessionId='rgvn0woibmh4g14u4z4nyssc']
```



## 2.LWPCookieJar

用`python`的`http`套件`cookiejar`的`library`，其中`FileCookieJar`的類別，主要將RequestCookieJar物件的瀏覽器的`cookie`記錄，用檔案的方式做保存，將Set-Cookie在瀏覽器的描述內容，儲存到檔案裡。

```python
impoty requests
import http.cookiejar as cj

#建立requests的session物件
crawler = requests.Session()
url = '登入的網址'

formtable={
    'account':'',
    'password':''
}
formtable['account'] = '帳號'
formtable['password'] = '密碼'

#data就是帶帳號密碼的資料進行登入
crawler.port(url, data = formtable)
cookies = crawler.cookies

#檔案名稱為cookies.txt
cookies_file = 'cookies.txt'
jar = cj.LWPCookieJar()

#設定session物件的cookie資料
for c in cookies:
    jar.set_cookie(c)

#執行儲存
jar.save(filename='cookies_scan.txt', ignore_discard=True, ignore_expires=True)
```



## 3.MozillaCookieJar

`MozillaCookieJar`與`LWPCookieJar`一樣都是來自於`python`的`http`套件`cookiejar`的`FileCookieJar`的類別，這兩種都是將cookie資料用檔案的方式做保存，差在於檔案存的內容不一樣。



## 4.SimpleCookieJar







## 循序圖

![CookieJarUML](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/CookieJarUML.png)

將cookie資料傳到首頁改成傳到瀏覽器上



## 流程圖

![sport-cookie](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/sport-cookie.png)

參考資料:



