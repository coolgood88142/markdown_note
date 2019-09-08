---
title: "爬蟲"
date: "2019-09-09"
author: "python"
summary: "說明爬蟲原理"
---

什麼是爬蟲?是通過網頁的鏈接網址，模擬瀏覽器用GET或POST方式請求(Request)到Server端，當Server端接受後回傳網頁內容。

使用爬蟲時我們會加入headers中的User-Agent來表示我們使用瀏覽器進行請求。

headers包含html版本、瀏覽器、編碼、伺服器資訊等，用來提供 request 或 response 的資訊。

當我們得到HTML內容後，解析內容透過字串處理、正規化等過濾抓取我們要的資料。



### 架構

![crawler](C:\Users\user\Desktop\crawler.png)



參考資料:

<http://yanlong4869.blogspot.com/2015/09/python-crawler.html>、

[https://medium.com/dualcores-studio/python-x-%E7%B6%B2%E8%B7%AF%E7%88%AC%E8%9F%B2-c30ffda0ad78](https://medium.com/dualcores-studio/python-x-網路爬蟲-c30ffda0ad78)



