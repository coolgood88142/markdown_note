## Chrome 套件結合富果API

### 什麼是Chrome 套件?

主要是擴充google瀏覽器的功能，讓瀏覽器顯示某個網站的通知、變更某個網站的CSS等等。Chrome套件本身也是以JavaScript、HTML或是CSS等網路技術寫成。

### 什麼是中央氣象局API?

中央氣象局提供的開放式資料，我們可以利用API快速拿到氣象局的最新資訊。

使用之前要先到[氣象資料開放平台](https://opendata.cwb.gov.tw/index)註冊帳號，登入之後點選取得授權碼

![weather](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/weather.PNG>)

再到開放資料平台的API網站，用授權碼測試取得資料，成功之後，下方有可以把整個json資料下載下來看。

![weather-5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/weather-5.PNG>)

![weather-6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/weather-6.PNG>)

以下示範API的寫法

```
https://opendata.cwb.gov.tw/api/各種天氣資訊的url?Authorization=氣象api的token&locationName=縣市名稱
```

這裡有各種氣象資料，例如：未來兩天的天氣預報等等。

### 範例

例如：顯示台灣地圖滑鼠滑過去顯示該縣市的當天天氣資訊、在做一個搜尋縣市當天的氣象資訊，搜尋後開啟新分頁用表格方式呈現，會有checkbox 勾選未來兩天的天氣預報、降雨量、風速、酸雨PH值、地震等等

