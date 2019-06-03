---
title: "Sitemap介紹"
date: "2019-05-12"
author: "sitemap"
summary: "說明如何使用sitemap"
---

什麼是SiteMap?是提供網站中網頁、影片和其他檔案的相關資訊，可使用網頁格式撰寫(HTML、XML等)，可使用SEO(搜尋引擎優化)，快速搜尋到自己的網站。



SiteMap做什麼用的?有利快速搜尋網站中的資訊，建立sitemap時大部分用XML格式，使用範圍廣泛，但XML節點內容有一定的規範，才可解析內容取得網站資訊



可以做什麼?使用sitemap可讓網站全部標籤建立索引，要先提交sitemap檔案給網站管理工具(google、yahoo)，之後別人在瀏覽器可以用搜尋關鍵字時，因為有使用sitemap，別人快速找到我的網站，但提交sitemap時都需要時間才會看到我的網站在前面幾筆。



![question_hugo](https://coolgood88142.github.io/images/question_hugo.png)

目前以XML格式居多，以下範例介紹SiteMap

## SiteMap標籤介紹

#### **urlset xmlns** ：主要是指定XML的版本

#### **url** ：指定要網址

#### **loc** ：指定要搜尋網站某個分頁完整的網址

#### **lastmod**：頁面最後更新的時間

#### **changefreq**：表示「這個頁面」的更新頻率，並非以整個網站的更新頻率撰寫

![sitemap](https://coolgood88142.github.io/images/sitemap.png)





## **Sitemap自動產生工具**

產生sitemap的工具有幾百種，以上兩種工具都可以自動產生sitemap

[Screaming Frog SEO Spider](https://www.seoseo.com.tw/article_detail_524.html)

[Online XML Sitemap Generator](https://www.seoseo.com.tw/article_detail_559.html)





## 大型網站的Sitemap要如何製作

在大型網站要製作sitemap時所要考量的因素與小型網站大大的不同。這時候往往會遇到以下這些問題：

### 1. 網址數量往往超過一個Sitemap檔案可以乘載的大小

　　根據Google的Sitemap官方指南，每個Sitemap檔案內網址的極限數量只能有50,000 個。因此當網站規模大於這個大小時，可以考慮在製作Sitemap時用創建目錄檔(sitemap_index)的方式，如此一來可以對各個Sitemap檔案進行管理。

### 2. 單靠Sitemap製作工具依然會有許多網頁沒辦法被找到

　　這是因為Sitemap的製作工具其理解網站的方式一樣是依靠Crawler在網站中找出所有的網頁。因此可以理解成用Sitemap製作工具找到的網頁基本上都是google本身就有辦法找到的，但是Sitemap最大的功能就是讓搜尋引擎能夠在網站中發現它無法找到的網頁，因此若只單單使用這些工具對網站結構越複雜、越大型的網站是絕對不夠的。

### 3. 每天都有大量新頁面產生，需要時時更新Sitemap內的內容

　　一般來說使用Sitemap工具並沒有辦法時時刻刻更新Sitemap的內容，而每一次更新時都會需要機器人在自己的網站上重新爬取一次所有的連結，相當沒有效率，尤其不可能只要有新的內容就重新操作一次這樣的動作。

簡單來說sitemap就像地址、門牌號碼這樣意思，可以清楚讓快遞清楚到這地方收信，那Google這邊 郵差就是爬蟲，可以輕鬆讓爬蟲把我們地址帶回去資料庫紀錄。



## Sitemap.xml檔案的提交

以下使用 [Google Search Console](https://search.google.com/search-console) 為例提交 sitemap。

提交sitemap之前，請先建立`robots.txt`檔案，要透過檔案讓別人知道sitemap的網址，才可以快速搜尋到。

例如：部落格網址(https://coolgood88142.github.io/zh-tw)

```
User-agent *
Sitemap:https://coolgood88142.github.io/zh-tw/sitemap.xml
```

以下使用 [Google Search Console](https://search.google.com/search-console) 為例提交 sitemap。

填寫 `robots.txt` 網址測試

![search1](https://coolgood88142.github.io/images/search1.png)

填寫 `sitemap.xml` 網址測試，如果測試結果正常，就可以正式提交了！，過一段時間search console 就可以看到網頁被收錄的狀態。

![search2](https://coolgood88142.github.io/images/search2.png)

## 問題

一個網站有沒有sitemap差在哪?



參考資料:

[https://www.eztrust.com.tw/html/faq/qa_show.aspx?id=125](https://codertw.com/程式語言/540757/)、

<https://www.awoo.com.tw/blog/2018/01/sitemap-xml/>、

<https://www.seoseo.com.tw/article_detail_637.html>、

<https://www.seoseo.com.tw/article_detail_524.html>