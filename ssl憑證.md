---
title: "SSL憑證"
date: "2019-09-01"
author: "SSL"
summary: "說明SSL憑證原理"
---

什麼是SSL?是Secure Socket Layer（安全套接層協議）的縮寫，可以在Internet上提供秘密性傳輸。SSL目前已廣泛的應用在HTTP連線上，當以「https://」方式連上網站，即代表該網站有支援SSL。使用 SSL 技術將資料加密，然後傳送到伺服器。加密是將雜亂無章的資料編寫為無法解譯之格式的一種程序，加密的資料可以透過適當的解密金鑰回傳為可以讀取的格式。



## SSL憑證如何保護你的資料

使用SSL憑證設計一組編碼連結於使用者的網頁瀏覽器與網頁伺服器之間，進行資料加密，當瀏覽器將資料傳到伺服器時，SSL憑證可保護資料，以避免被其他網路的某些人監控，需要解密才可知道資料內容，所以即使被攔截封包資料或竊取，沒有金鑰解密也無法知道內容。



## SSL憑證對網頁訪客意味著什麼?

大部分的SSL憑證，包含了網址、公司名稱、地址、城市、州與國家，也包含了憑證到期日與權證公司細節(SSL發行公司)，當瀏覽器嘗試連結網站建立一個SSL連結時，連結中會核對SSL憑證是未到期的、發佈於信賴的授權機構並且被使用在正確的網站上。如果核對的任何一個是錯誤的，你的的網頁瀏覽器會顯示錯誤訊息給使用者知道，這個網站的SSL憑證是不安全的。



## SSL連線四個步驟

![SSL連線](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/SSL連線.png)



- SSL Client端發出ClientHello給SSL伺服器端。告知伺服器端本身可實現的算法列表和其他一些需要的資訊。 
- SSL的服務器端在接收ClientHello後會回應一個ServerHello，裡面確定了這次通訊所需要的演算法，並送出伺服器本身的憑證（資訊內包含身分及公鑰）。 
- SSL Client會新增一個秘密金鑰，並利用伺服器傳來的公鑰來加密，而且會回傳加密後的秘密金鑰密文給伺服器。 

- 伺服器使用自己的私鑰解開秘密金鑰密文，取得秘密金鑰後，即利用此秘密金鑰來相互通訊。 



## 範例：xampp建立SSL憑證

補上建立SSL憑證步驟與圖片





參考資料:

<https://www.netadmin.com.tw/netadmin/zh-tw/technology/6F6D669EB83E4DC9BEA42F1C94636D46>>、

<https://www.newscan.com.tw/all-seo/what-ssl-certificate.htm>

