---
title: "python"
date: "2019-09-11"
author: "python"
summary: "說明爬蟲原理"
---

以下使用python 將會員系統，使用POST提交方式，將登入後的頁面標籤做回傳

介紹Form表單與java script的FormData兩種

### 架構

![Form表單](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/Form表單.png>)

![FormData](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/FormData.png>)



### Form表單

```
import requests

# 建立表頭
headers = '''Accept:*/*
Accept-Encoding:gzip, deflate, br
Accept-Language:zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7
Connection:keep-alive
Host:scr.cyc.org.tw
Referer:https://scr.cyc.org.tw/tp10.aspx?module=login_page&files=login
User-Agent:Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36
X-Requested-With:XMLHttpRequest'''

# 建立帳號、密碼、驗證碼的物件
account='帳號'
password='密碼'
formtable={
    'loginid':'',
    'loginpw':'',
    'Captcha_text':''
}

formtable['loginid'] = account
formtable['loginpw'] = password

# 截取會員系統的驗證碼，進行匯出圖檔(ver_pic.png)
pic = session_requests.get('https://scr.cyc.org.tw//NewCaptcha.aspx').content
with open('ver_pic.png','wb') as f:
    f.write(pic)

# 執行時會要求輸入驗證碼，在專案路徑看驗證碼圖檔並輸入
vercode=input('請輸入驗證碼：')
formtable['Captcha_text']=vercode

# 建立requests套件的Session
session_requests = requests.Session()

# 將登入系統用POST方式，提交帳密資訊與表頭進行登入
url='https://scr.cyc.org.tw/tp10.aspx'
res = session_requests.post(url+'?module=login_page&files=login', data = formtable, headers = login_header)

# 登入後Session有記錄到，在導頁到登入後的頁面並將截取頁面標籤
sign = session_requests.get(url+'?module=ind&files=ind')
print(sign.text)

```



### FormData

```
import requests
from requests_toolbelt  import MultipartEncoder

# 建立表頭
headers = '''Accept:*/*
Accept-Encoding:gzip, deflate, br
Accept-Language:zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7
Connection:keep-alive
Content-Type:
Host:scr.cyc.org.tw
Referer:https://scr.cyc.org.tw/tp10.aspx?module=login_page&files=login
User-Agent:Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36
X-Requested-With:XMLHttpRequest'''

# 建立帳號、密碼、驗證碼的物件
account='帳號'
password='密碼'
formtable={
    'loginid':'',
    'loginpw':'',
    'Captcha_text':''
}

formtable['loginid'] = account
formtable['loginpw'] = password

# 截取會員系統的驗證碼，進行匯出圖檔(ver_pic.png)
pic = session_requests.get('https://scr.cyc.org.tw//NewCaptcha.aspx').content
with open('ver_pic.png','wb') as f:
    f.write(pic)

# 執行時會要求輸入驗證碼，在專案路徑看驗證碼圖檔並輸入
vercode=input('請輸入驗證碼：')
formtable['Captcha_text']=vercode

# 將login_header的Content-Type，把帳密資訊建立FormData物件
formdata = MultipartEncoder(fields=formtable)
login_header['Content-Type'] = formdata.content_type

# 建立requests套件的Session
session_requests = requests.Session()

# 將登入系統用POST方式，提交帳密資訊與表頭進行登入
url='https://scr.cyc.org.tw/tp10.aspx'
res = session_requests.post(url+'?module=login_page&files=login', data = formdata, headers = login_header)

# 登入後Session有記錄到，在導頁到登入後的頁面並將截取頁面標籤
sign = session_requests.get(url+'?module=ind&files=ind')
print(sign.text)
```



參考資料:

<https://scr.cyc.org.tw/tp10.aspx?module=login_page&files=login>、

https://imququ.com/post/four-ways-to-post-data-in-http.html、

<https://www.jianshu.com/p/902452189ca9>



