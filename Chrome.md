# 製作 Chrome 套件到上架商店

### 大綱

- 什麼是Chrome 套件
- 製作Chrome套件
  - 搜尋youtube的影片
- 開發Chrome套件
  - Manifest
  - Background Script
  - UI Elements

- 上傳 Chrome 套件
- 上架 Chrome Web Store
  - 前往Chrome 線上應用程式商店開發人員主頁
  - 上傳套件
  - 填寫和提供上架商店所需資訊
- 參考資料

### 什麼是Chrome 套件

主要是擴充google瀏覽器的功能，讓瀏覽器顯示某個網站的通知、變更某個網站的CSS等等。Chrome套件本身也是以JavaScript、HTML或是CSS等網路技術寫成。

### 製作Chrome套件

#### 搜尋youtube的影片

使用者在輸入完影片的關鍵字後，點選「Search」按鈕，網頁會新增一個分頁，獲取youtube查詢關鍵字的所有相關影片

![chrome1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome1.png>)

![chrome2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome2.png>)

### 	開發Chrome套件

開發套件之前，先介紹幾個套件的元素

#### Manifest

一個 JSON 格式的檔案 — 名為 manifest.json — 包含了套件資訊：例如版本、名稱、介紹、提供的檔案，及相關權限的設定。

- name：套件名稱
- version：版本號
- description：套件描述
- permissions：設定套件會使用到的 Chrome API 和網頁權限
- icons：套件將使用到的各尺寸的圖像檔

```json
{
  "name": "Search Youtube Video",
  "version": "1.2",
  "description": "Quickly Search Youtube Video",
  "permissions": [],
  "icons": {
    "16": "images/image16.png",
    "32": "images/image32.png",
    "48": "images/image48.png",
    "128": "images/image128.png"
  },
 ...
```

#### Background Script

設定瀏覽器上監聽事件，這裡要注意即使沒有要設定監聽事件，但是一定要給一個空的js檔案，不然套件會無法載入

在manifest.json檔案設定background屬性

- script：設定使用哪個腳本檔案
- persistent：設定腳本的開關

```json
...
"background": {
    "scripts": [
      "js/background.js"
    ],
    "persistent": false
  },
...
```

#### UI Elements

套件常見的 兩個UI：

- Browser Action：套件只能在特定頁面上被啟用
- Page Action：套件能在所有的頁面上被啟用

在網址列右邊的小 icon，顯示著套件是否已被啟用，點擊 icon 後跳出的一個 HTML 的視窗

![chrome3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome3.png>)

在manifest.json檔案設定browser_action 屬性

- default_popup：設定 Popup 的 HTML 檔案
- default_icon：設定google瀏覽器的解析度大小，顯示對應的icon

```json
"browser_action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "images/image16.png",
      "32": "images/image32.png",
      "48": "images/image48.png",
      "128": "images/image128.png"
    }
  },
```

### 上傳 Chrome 套件

- 打開chrome的擴充功能或在 Chrome 網址列輸入 chrome://extensions/
- 開啟「開發人員模式」
- 點選「載入未封裝項目」，選擇套件的資料夾
- 點選右上角的擴充功能，將套件固定在瀏覽器上

![chrome4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome4.png>)

![chrome5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome5.png>)

如果要Debug測試的話，在icon用滑鼠右鍵可以打開檢查視窗

![chrome6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome6.png>)

### 上架 Chrome Web Store

#### 前往[Chrome 線上應用程式商店開發人員主頁](https://chrome.google.com/webstore/devconsole/register?hl=zh-TW)

要支付5美元的註冊費用

![chrome7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome7.png>)

#### 上傳套件

這裡要注意，上傳的格式只接受 ZIP 檔案，要將套件壓縮後上傳

#### 填寫和提供上架商店所需資訊

提供給chrome線上應用程式商店，要填寫套件介紹、Icon、宣傳圖、類型、上架國家、收費與否、語言等資訊，寫完後上傳即可。

### 範例

製作搜尋Youtube影片，以及Youtube的影片資訊

頁面

```html
<body>
  <!--Find Royalty Free Viedo-->
  <section class="bg-white border rounded p-2">
    <form id='search-viedo'>
      <legend class='text-secondary'>搜尋Youtube影片</legend>
      <!--Second row-->
      <div class="input-group input-group-sm my-2 ">
        <input type="text" class="form-control form-control-sm" id="search-string" placeholder="keyWord" required>
        <div class="input-group-append">
          <button class="btn btn-primary" type="submit" id='open-all'>搜尋</button>
        </div>
      </div>

      <div id="viedo"></div>
    </form>
  </section>
</body>
```

Manifest.js

```javascript
{
  "name": "Search Youtube Video",
  "version": "1.2",
  "description": "Quickly Search Youtube ",
  "permissions": [
    "contextMenus",
    "activeTab",
    "https://www.youtube.com/"
  ],
  "browser_action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "images/image16.png",
      "32": "images/image32.png",
      "48": "images/image48.png",
      "128": "images/image128.png"
    }
  },
  "background": {
    "scripts": [
      "js/background.js"
    ],
    "persistent": false
  },
  "icons": {
    "16": "images/image16.png",
    "32": "images/image32.png",
    "48": "images/image48.png",
    "128": "images/image128.png"
  },
  "content_scripts": [
    {
      "matches": [
        "<all_urls>"
      ],
      "js": [
        "js/axios.min.js",
        "js/content.js"
      ]
    }
  ],
  "manifest_version": 2
}
```







```javascript
window.onload = function () {
  let clickedViedo = null
    
  function generateReport(request, sender, sendResponse) {
    // show error message if getUrl is false or clickImg is null
    if (!request.getUrl || !clickedViedo) { return showErrorMessage() }
    // generate tbody
    //根據目前的網址來確認是否為YT，不是YT就顯示訊息，如果是YT的嵌入式影片要另外抓資料
    if(clickedViedo.baseURI.indexOf('https://www.youtube.com/watch') != -1){
      generateTbody(location.href.indexOf('='))
    }else if(clickedViedo.localName == 'iframe'){
      generateTbody(clickedViedo.src.indexOf('='))
    }
    // generate the report with tbody
    // generateTable(tbodyInnerHTML)
    // listen to url click to copy url
    document.querySelectorAll('.url').forEach(link => link.addEventListener('click', copyUrl))
    // listen to reload button click to lead user back to image page
    document.getElementById('reload').addEventListener('click', () => location.reload())
  }

  // listen to contextmenu being opened and save the target image
  document.addEventListener('contextmenu', event => clickedViedo = event.target)


  // listen to message request from the extension: background.js
  chrome.runtime.onMessage.addListener(generateReport)

}
```

https://developer.chrome.com/docs/extensions/reference/contextMenus/

https://ingtt.com/10317/youtube-embed-autoplay



### 參考資料

- https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E5%BE%9E%E9%9B%B6%E9%96%8B%E5%A7%8B%E8%A3%BD%E4%BD%9C-chrome-%E5%A5%97%E4%BB%B6%E5%88%B0%E4%B8%8A%E6%9E%B6%E5%95%86%E5%BA%97-4971ed79ac77
- https://github.com/smallpaes/find-placeholder-image
