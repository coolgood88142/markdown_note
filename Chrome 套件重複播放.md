# 用Youtube Data API 製作 Chrome 套件

### 大綱

- 什麼是Chrome 套件
- 什麼是Youtube Data ApI (v3)
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

### 什麼是Youtube Data ApI (v3)

目前Youtube Data ApI最新的版本為v3，API有分為4種

- Subscriptions.insert：用API去訂閱Youtuber
- channels.list：用API查詢播放列表
- search.list：用API搜尋影片資料
- videos.update：上傳自己的影片

### 製作Chrome套件

#### 搜尋youtube的影片

使用者在輸入完影片的關鍵字後，點選「搜尋」按鈕，網頁會新增一個分頁，獲取youtube查詢關鍵字的所有相關影片

![chrome1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome1.png>)

![chrome2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome2.png>)



![chrome9](C:\xampp\htdocs\markdown_note\assets\images\chrome9.png)



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
  "manifest_version": 2
 ...
```

manifest目前v2、v3版本，如果是自行開發自己用的話，用v2即可，但是要上架的話，就必須要用到v3。

chrome管理員有提有v2改用v3的寫法，[參考資料](https://developer.chrome.com/docs/extensions/mv3/intro/mv3-migration/)

Manifest V2與Manifest V3，在Manifest.json的差異



|                          | Manifest V2                         | Manifest V3                                                  |
| ------------------------ | ----------------------------------- | ------------------------------------------------------------ |
| manifest_version         | 2                                   | 3                                                            |
| permissions              | chrome擴充功能的API物件、特定的網址 | chrome擴充功能的API物件、特地的網址需要額外建一個host_permissions來做設定 |
| Content security policy  |                                     | extension_pages、sandbox                                     |
| action                   | browser_action、page_action         | action                                                       |
| web_accessible_resources | RESOURCE_PATHS                      | resources": [RESOURCE_PATHS],   "matches": [MATCH_PATTERNS],   "extension_ids": [EXTENSION_IDS],   optional "use_dynamic_url": boolean |

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

![chrome8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/chrome8.png>)

#### 上傳套件

這裡要注意，上傳的格式只接受 ZIP 檔案，要將套件壓縮後上傳

#### 填寫和提供上架商店所需資訊

提供給chrome線上應用程式商店，要填寫套件介紹、Icon、宣傳圖、類型、上架國家、收費與否、語言等資訊，寫完後上傳即可。

### 範例

製作搜尋Youtube影片，以及顯示Youtube的影片資訊

#### pupop.html

```html
<body>
  <!--Find Royalty Free Viedo-->
  <section class="bg-white border rounded p-2">
    <form id='search-viedo'>
      <legend class='text-secondary'>搜尋Youtube影片</legend>
      <!--Second row-->
      <div class="input-group input-group-sm my-2 ">
        <input type="text" class="form-control form-control-sm" id="search-string" placeholder="請輸入關鍵字" required>
        <div class="input-group-append">
          <button class="btn btn-primary" type="submit" id='searchVideo'>搜尋</button>
        </div>
      </div>
	  <input id="YouTube-video-id" type="hidden" value="" size="12" pattern="[_\-0-9A-Za-z]{11}" onchange="youTubePlayerChangeVideoId();">
      <div id="viedo"></div>
    </form>
  </section>
</body>
```

#### Manifest.js

```javascript
{
  "name": "Search Youtube Video",
  "version": "1.2",
  "description": "Quickly Search Youtube ",
  "permissions": [
    "contextMenus"
  ],
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "images/image16.png",
      "32": "images/image32.png",
      "48": "images/image48.png",
      "128": "images/image128.png"
    }
  },
  "background": {
    "service_worker": "js/background.js"
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
  "manifest_version": 3
}
```



#### popup.js

```javascript
const searchViedoForm = document.getElementById('search-viedo')
const searchString = document.getElementById('search-string')
const key = 'youtube data api key'
let url = 'https://www.googleapis.com/youtube/v3/search?part=snippet&type=video&key=' + key

const youTubePlayer;
function imgClickVideo(img, index) {
  img.addEventListener('click', function() {
    let playVideoId = document.getElementById('playVideoId' + index)
    youTubePlayer.cueVideoById({suggestedQuality: 'tiny',
                                videoId: playVideoId.value
                               });
    youTubePlayer.pauseVideo();
    document.getElementById('videoPlayerImg').src = this.src
    document.getElementById('videoData').style.display = 'none'
    document.getElementById('videoPlayer').style.display = ''
  })
}

function onYouTubeIframeAPIReady() {
  'use strict';

  var inputVideoId = document.getElementById('YouTube-video-id');
  var videoId = inputVideoId.value;
  var suggestedQuality = 'tiny';
  var height = 180;
  var width = 320;
  var youTubePlayerVolumeItemId = 'YouTube-player-volume';


  function onError(event) {
      youTubePlayer.personalPlayer.errors.push(event.data);
  }


  function onReady(event) {
      var player = event.target;

      player.loadVideoById({suggestedQuality: suggestedQuality,
                            videoId: videoId
                           });
      player.pauseVideo();
  }


  function onStateChange(event) {
      var volume = Math.round(event.target.getVolume());
      var volumeItem = document.getElementById(youTubePlayerVolumeItemId);

      if (volumeItem && (Math.round(volumeItem.value) != volume)) {
          volumeItem.value = volume;
      }
  }


  youTubePlayer = new YT.Player('YouTube-player',
                                {videoId: videoId,
                                 height: height,
                                 width: width,
                                 playerVars: {'autohide': 0,
                                              'cc_load_policy': 0,
                                              'controls': 2,
                                              'disablekb': 1,
                                              'iv_load_policy': 3,
                                              'modestbranding': 1,
                                              'rel': 0,
                                              'showinfo': 0,
                                              'start': 3
                                             },
                                 events: {'onError': onError,
                                          'onReady': onReady,
                                          'onStateChange': onStateChange
                                         }
                                });

  // Add private data to the YouTube object
  youTubePlayer.personalPlayer = {'currentTimeSliding': false,
                                  'errors': []};
}

function youTubePlayerActive() {
  'use strict';

  return youTubePlayer && youTubePlayer.hasOwnProperty('getPlayerState');
}


/**
 * Pause.
 */
 function youTubePlayerPause() {
  'use strict';

  if (youTubePlayerActive()) {
      youTubePlayer.pauseVideo();
  }
}

/**
 * Play.
 */
 function youTubePlayerPlay() {
  'use strict';

  if (youTubePlayerActive()) {
      youTubePlayer.playVideo();
  }
}

//建立分頁事件，每下一頁或上一頁，會拿到nextPageToken或prevPageToken
const li = document.getElementsByTagName("li");
for( var x=0; x < li.length; x++ ) {
  li[x].onclick = function(){
    url = url + '&q=' + searchString.value
    if(this.id == 'Next' && $('#nextPageToken').val() != ''){
      setVideoInfo(url, $('#nextPageToken').val(), function(items){
        setVideoHtml(items)
      })
    }else if(this.id == 'Previous' && $('#prevPageToken').val() != ''){
      setVideoInfo(url, $('#prevPageToken').val(), function(items){
        setVideoHtml(items)
      })
    }
  }
}


//搜尋影片關鍵字事件
searchViedoForm.addEventListener('submit', event => {
  event.preventDefault()
  let url = 'https://www.googleapis.com/youtube/v3/search?part=snippet&type=video&key=' + key + '&q=' + searchString.value
  setVideoInfo(url, null, function(items){
    setVideoHtml(items)
    document.getElementsByClassName('isPagination')[0].style['display'] = 'inline'
  })
 
//設定影片關鍵字，打api抓影片資料
function setVideoInfo(url, pageToken, callBackFunction){
  if(pageToken != null){
    url += '&pageToken=' + pageToken
  }

  axios.get(url).then((response) => {
    let items = response.data.items

    //更換按鈕的token的資料
    $('#nextPageToken').val(response.data.nextPageToken)
    $('#prevPageToken').val(response.data.prevPageToken)
      
    if(callBackFunction != null){
      callBackFunction(items)
    }
  
  }).catch((error) => {
    if (error.response) {
      console.log(error.response.data)
      console.log(error.response.status)
      console.log(error.response.headers)
    } else {
      console.log("Error", error.message)
    }
  })
}
    
function setVideoHtml(items) {
  let context = ''
  let videoId = []
  let videoObj = []
  let videoImg = []
  items.forEach((el, index) => {
    //發佈日期用的格式轉換
    let date = new Date(el.snippet.publishTime);

    //這裡要改用createElement的方式建立，之後要定義圖片元件要使用點擊事件
    // const div = document.createElement('div');
    // div.setAttribute('class', 'style-scope ytd-item-section-renderer');
    context += 
      		  '...影片標籤'+
              '<div id="channel-info" class="style-scope ytd-video-renderer">' +
                '<h6>發佈者：' + el.snippet.channelTitle +'</h6>' +
              '</div>' + 
              '<div class="metadata-snippet-container style-scope ytd-video-renderer">' +
                '<small>影片資訊：'+ el.snippet.description +'<small/>' +
                '<p>發佈日期：'+ date.getFullYear() + "-" + ((date.getMonth() + 1) < 10 ? "0" + (date.getMonth() + 1) : date.getMonth() + 1) + "-" + (date.getDate() < 10 ? "0" + date.getDate() : date.getDate()) +'<p/>' +
              '</div>' +
            '</div>' +  
        '</div>' +  
      '</div>';   

      videoId.push(el.id.videoId)
      videoObj.push(new Object())
      videoImg.push(el.snippet.thumbnails.medium.url)
  });

  document.getElementById("viedo").innerHTML = context    
  chrome.storage.local.clear()
  chrome.storage.local.set({
    "body": context,
    "videoImg": videoImg
  })

  const video = document.getElementsByTagName('iframe')
  const count = video.length


  if(video != undefined && count > 0){
    imgClickVideo(document.getElementById('playVideoImg0'), 0)
    '...'
  }

  chrome.runtime.sendMessage({
    saveState: true, 
    state: document.getElementsByTagName("body")[0],
  })
}

const videoPlay = document.getElementById('play_pause')
videoPlay.addEventListener('click', () => {
  if(document.getElementById("playImg").style.display == 'none'){
    document.getElementById("playImg").style.display = ''
    document.getElementById("pauseImg").style.display = 'none'
    youTubePlayerPause()
  } else if(document.getElementById("pauseImg").style.display == 'none'){
    document.getElementById("playImg").style.display = 'none'
    document.getElementById("pauseImg").style.display = ''
    youTubePlayerPlay()
  }
});

const backVideoData = document.getElementById('backVideoData')
backVideoData.addEventListener('click', () => {
  document.getElementById('videoPlayerImg').src = ''
  document.getElementById('videoData').style.display = ''
  document.getElementById('videoPlayer').style.display = 'none'
})

```

#### background.js

```javascript
// 建立顯示右鍵事件，網頁上任一個地方，都會顯示showVideoInfo
chrome.contextMenus.create( {  
  "id": "showVideoInfo",
  "title": "ShowVideoInfo",
  "contexts": ["all"] 
})

//建立右鍵點擊showVideoInfo事件
chrome.contextMenus.onClicked.addListener(function (info, tab) {
    //判斷右鍵上列表是否為showVideoInfo
    if (info.menuItemId === "showVideoInfo") { 
      console.log(info)
      // send a message from the extension to content script of current tab
      chrome.tabs.query({ active: true, currentWindow: true }, tabs => {
        //發送目前分頁的物件
        chrome.tabs.sendMessage(tabs[0].id, { getUrl: true })
      })
    }else{
      return
    }
})
```

#### content.js

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

流程說明：開啟套件後，可輸入關鍵字後按搜尋，顯示關鍵字相關的Youtube影片資料，會影片名稱、發佈者、發佈日期、影片連結，另一個功能在Youtube影片，滑鼠右鍵點選「showVideoInfo」，會開啟新分頁顯示該影片的資訊，用表格呈現



背景失敗原因

1.chrome.runtime.onConnect 必須要在長時間監聽，才能在關閉頁面時，做觸發播放

   

2.html 上新增script 寫https://www.youtube.com/iframe_api 會發生作物

3.parentNode**.**insertBefore(tag, first_script_tag) 新增一個script物件 引用youtube iframe api

4.在瀏覽器的body appendChild 新增一個script物件 引用youtube iframe api

5.要利用youtube iframe api得到目前影面的播放時間





https://developer.chrome.com/docs/extensions/reference/contextMenus/

https://ingtt.com/10317/youtube-embed-autoplay



### 參考資料

- https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E5%BE%9E%E9%9B%B6%E9%96%8B%E5%A7%8B%E8%A3%BD%E4%BD%9C-chrome-%E5%A5%97%E4%BB%B6%E5%88%B0%E4%B8%8A%E6%9E%B6%E5%95%86%E5%BA%97-4971ed79ac77
- https://github.com/smallpaes/find-placeholder-image
- https://developer.chrome.com/docs/extensions/mv3/intro/mv3-migration/
