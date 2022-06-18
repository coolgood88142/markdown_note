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

![sitemap](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/sitemap.png)

#### **lastmod**：頁面最後更新的時間

```
<lastmod>2005-01-01</lastmod>
```

#### **changefreq**：表示「這個頁面」的更新頻率，並非以整個網站的更新頻率撰寫

##### 以下是頻率提供一般的資訊給搜尋引擎

- always ：表示頁面一直在變動，更新頻率非常高。新聞網站就會選擇這種標籤值。
- hourly：每小時會變動。
- daily：每天會變動。
- weekly：每周會變動。**一般商家可以選擇這個標籤。**
- monthly：每月會變動。月刊網站或者推出新產品、文章周期較長的網站可以使用這個標籤。
- yearly：每年會變動。
- never：永不變動。

`always` 每次存取而變更的文件，網路爬蟲不會隨時來爬。`never` 描述已封存的 URL。

## **Sitemap自動產生工具**

產生sitemap的工具有幾百種，以上兩種工具都可以自動產生sitemap

[Screaming Frog SEO Spider](https://www.seoseo.com.tw/article_detail_524.html)、[Online XML Sitemap Generator](https://www.seoseo.com.tw/article_detail_559.html)

以下介紹Screaming Frog SEO Spider

### 1.輸入要建立sitemap的網址後按start，例如：[Yahoo](https://tw.yahoo.com/)

![sitemap1](C:\xampp\htdocs\markdown_note\assets\images\sitemap1.png)

2.上方sitemaps底下的XML sitemap，選擇匯出的sitemp需要哪些資料，另存sitemap檔案

![sitemap2](C:\xampp\htdocs\markdown_note\assets\images\sitemap2.png)

### **說明選項**

#### pages: noindex pages 排除網址、Canonicalised 建立規範化URL，移除重複的URL、paginated URLs 建立分頁的URL Response codes 檢查網址有包含什麼錯誤代碼 例如:404

#### Last Modified:修改日期

#### priority:建立優先度順序

#### Change Frequerency:建立更新頻率順序

#### Image:建立圖片索引

#### Hrflang:

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

以下使用 [Google Search Console](https://search.google.com/search-console) 為例提交 sitemap；目前Google Search Console有區分舊版與新版的部分，新版暫時沒有robots.txt檔案測試工具。

提交sitemap之前，請先將`robots.txt`檔案建立在專案的根目錄裡，要透過檔案讓別人知道sitemap的網址，才可以快速搜尋到。

例如：部落格網址(https://coolgood88142.github.io/)

`robots.txt`的內容 

```
User-agent *
Sitemap:https://coolgood88142.github.io/zh-tw/sitemap.xml
```

填寫 `robots.txt` 點選測試，成功會顯示已允選

![search1](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/search1.png)

將自己的上傳 `sitemap.xml` 提交上傳，狀態顯示成功就OK了。

![search2](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/search2.png)

## 問題

#### 一個網站有沒有sitemap差在哪?

首先，一個網站對於電腦來説就是很多頁沒有排序的紙，而一本書與一堆紙的分別就在於紙是散亂的，而書是經過整理排序，所以我們的網站也需要一個排予的工具。

很多人覺得把網站建立起來以後，Google 等搜尋引擎便會自已找到，但外國有人做過測試。如果建立一個網站後什麼都不做，Google 最快也需要 22 天才能找出你的網站，而且只有很少的資料，所以建立網站需要不少能力。

#### Google Search Console 如何更新網址

**如果網站結構改變了、或者是換網址了-->請記得更新sitemap**

sitemap的更新頻率(changefreq)主要是更新索引，提高使用搜尋頻率，例如:新聞在同一個頁面不斷的更新內容，搜尋引擎會依據更新頻率確認什麼時候更新內容。

更新頻率(changefreq)不一定要寫，要依據頁面內容更新，是否要讓使用者快速搜尋到最新內容。

#### Google Search Console 建立索引卻沒提交sitemap

Google Search Console可先的輸入網址後，使用測試線上網址，提交sitemap時，正常系統會顯示「網址在 Google 服務中」，涵蓋範圍顯示「已提交並建立索引」。

如果涵蓋範圍顯示「已建立索引，但沒提交sitemap」，代表網址有包含圖片、影音，sitemap檔案並沒有包含，

[建立圖片、影音sitemap說明](<https://support.google.com/webmasters/answer/80471?hl=zh-Hant&ref_topic=4581190>)，內容要如何怎麼填寫。

#### Google Search Console 網址包含圖片，如果不建立sitemap會怎麼樣?

#### Google Search Console 涵蓋範圍:為什麼會有些網址要排除?

網站可能要包含圖片、影音等等，提交sitemap只有網址內容，但是網址內的東西卻沒提交上去，當Google Search Console建立索引，因為網只有少東西為提交，涵蓋範圍才會顯示「為什麼會有些網址要排除?」

#### Google Search Console Sitemap的類型為什麼有Sitemap、Sitemap索引?差別在哪?，確定要寫一起還是要分開?

參考資料:

[https://www.eztrust.com.tw/html/faq/qa_show.aspx?id=125](https://codertw.com/程式語言/540757/)、

<https://www.awoo.com.tw/blog/2018/01/sitemap-xml/>、

<https://www.seoseo.com.tw/article_detail_637.html>、

<https://www.seoseo.com.tw/article_detail_524.html>、

<https://www.sitemaps.org/zh_TW/protocol.html>、

<https://www.cool3c.com/article/106684>、

<https://www.awoo.com.tw/blog/new-searchconsole-introduction/>、

<http://foreignsun.blogspot.com/2018/07/SEO-1.html>、

<https://support.google.com/webmasters/answer/156184?hl=zh-Hant>
