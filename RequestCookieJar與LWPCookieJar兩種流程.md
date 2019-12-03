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
import requests
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

#設定表頭使用哪些瀏覽器
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36'}

#data就是帶帳號密碼的資料進行登入
crawler.port(url, data = formtable, headers = headers)
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

```python
import requests
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

#設定表頭使用哪些瀏覽器
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36'}

#data就是帶帳號密碼的資料進行登入
crawler.port(url, data = formtable, headers = headers)
cookies = crawler.cookies

#檔案名稱為cookies.txt
cookies_file = 'cookies.txt'
jar = cj.MozillaCookieJar()

#設定session物件的cookie資料
for c in cookies:
    jar.set_cookie(c)

#執行儲存
jar.save(filename='cookies_scan.txt', ignore_discard=True, ignore_expires=True)
```

ignore_discard是遺棄cookies記錄保存起來，ignore_expires 是如果cookie記錄已存在就直接覆蓋

#### 兩者檔案內容

LWPCookieJar

```python
#LWP-Cookies-2.0
Set-Cookie3: ASP.NET_SessionId=ux23jhd4d204fo1qt5ysrtre; path="/"; domain="scr.cyc.org.tw"; path_spec; discard; HttpOnly=None; version=0
```

MozillaCookieJar

```python
# Netscape HTTP Cookie File
# http://curl.haxx.se/rfc/cookie_spec.html
# This is a generated file!  Do not edit.

scr.cyc.org.tw	FALSE	/	FALSE		ASP.NET_SessionId	ux23jhd4d204fo1qt5ysrtre
```

`LWPCookie`是用標準的Set-Cookie格式記錄的cookie，`MozillaCookieJar`是兼容火狐(file fox)瀏覽器的cookie

在爬蟲的時候



## 4.SimpleCookie

 `SimpleCookie`是`python`的`http`套件 `cookies` 的`library`，是一個cookie物件，可存放key跟value，在requests套件可以用這個物件帶cookie參數做爬蟲 

```python
import requests
import http.cookies as ck

cookie = ck.SimpleCookie()
cookie['ASP.NET_SessionId'] = '5vys55k1ejyipd3efm4hed03'
cookie['ASP.NET_SessionId']['domin'] = 'scr.cyc.org.tw'
cookie['ASP.NET_SessionId']['path'] = '/'

print(cookie.output())
#Set-Cookie3: ASP.NET_SessionId=5vys55k1ejyipd3efm4hed03; path="/"; domain="scr.cyc.org.tw";
```



## 循序圖

![CookieJarUML](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/CookieJarUML.png)



## 流程圖

![sport-cookie](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/sport-cookie.png)

參考資料:

https://blog.csdn.net/qq_33689414/article/details/78553470、

https://www.zhyea.com/2016/09/15/python-spider-7-using-cookie.html、

https://stackoverflow.com/questions/27652543/how-to-use-python-requests-to-fake-a-browser-visit、

https://wiki.jikexueyuan.com/project/python-crawler-guide/the-use-of-cookie.html、

[https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/374579/](https://codertw.com/程式語言/374579/)



