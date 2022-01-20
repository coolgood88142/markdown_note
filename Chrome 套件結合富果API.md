## Chrome 套件結合富果API

### 什麼是Chrome 套件?

主要是擴充google瀏覽器的功能，讓瀏覽器顯示某個網站的通知、變更某個網站的CSS等等。Chrome套件本身也是以JavaScript、HTML或是CSS等網路技術寫成。

### 什麼是富果API?

[富果 API ](https://developer.fugle.tw/)是由 [時報資訊](https://info.infotimes.com.tw/) 與 Fugle 富果技術團隊共同開發，提供台股及時行情 。

還需要申辦玉山證券富果帳戶，才可以免費申請  API token 使用 Fugle 富果 API。

在申辦玉山證券富果帳戶，必須要有玉山銀行帳戶，開戶還需要7天作業時間。

![fugle](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fugle.PNG>)

#### 富果API有哪些股票資訊?

API類型分為線圖(chart)、統計資訊(Quote)、當天資訊(Meta)、當天成交資訊(Deatlls)。

![fugle-1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fugle-1.PNG>)

在拿資料前要做驗證，確認使用者輸入的股票代號，以下示範API的寫法


```
https://api.fugle.tw/realtime/v0/intraday/類型?symbolId=股票代號&apiToken=富果帳號的token
```

例如回傳的當天資訊資料，在data的meta裡，會記錄著股票的資料，顯示股票名稱、做什麼產業等等

### 範例

例如做一個搜尋股票當天的資訊，搜尋後開啟分頁用表格方式呈現，會有readio button 選擇即時資訊、統計資訊、當天資訊、當天成交資訊

