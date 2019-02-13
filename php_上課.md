1. ### 學習了什麼技術，並應用在哪

- PHP

- mysql

- JQuery

- - JQuery-datatable
  - JQuery-datepicker

- Highcharts

- Bootstrap

- CSS

- Html

- Wireframe

- Design Pattern

- OO Concept

- Git、SVN

- ajax

- XDebug

- VSCode terminal

- rwd(Responsive Web Design) 

應用在網頁或手機偵測螢幕大小時，可適當自動調整網頁樣式

1. ### 遇到的困難

- php:在行程列表功能原先用admin與user兩個php整併。
- jquery-datatable:傳POST參數回到前端。
- jquery-datepicker:日期樣式
- highcharts:傳POST參數回到前端。
- bootstrap:設計頁面樣式
- ajax:在分析表功能遇到傳置php後端，POST參數接收不到導致回傳資料不如預期，以及用回傳物件資料使用在分析表資料裡。

1. ### 如何解決

- php:在行程列表功能裡的jquery-datatable有些一樣的，有些是不一樣的，需要先看兩隻php相同的部分在哪，再用session參數判斷是否為admin呈現那些資料。否為admin呈現那些資料。
- jquery-datatable:在多個功能用表格方式顯示，必須先從後端抓資料至前端，然後在將資料顯示在頁面上。
- jquery-datepicker:研究API的function取得想要日期格式與樣式。
- highcharts:在分析表功能從後端抓置前端，然後迴圈或php變數帶入到function底下產生圖表資料。
- bootstrap:研究bootstrap在div裡的class的排版
- ajax:在分析表功能裡先確認post參數裡的data是否有傳到目的，成功回傳時資料物件定義變數陣列組在裡面，對應在分析表的資料裡。

### 用到的開發環境

- VSCode
- Sublime text 3
- XAMPP