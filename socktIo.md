#### **socket.io**

**目錄**

- 介紹
- 特點
- 應用

#### 介紹

在講socket.io之前要先介紹WebSockets

WebSockets是一個可以瀏覽器與伺服器進行一段互動通訊的網路協定，socket.io就是用這種方式，可以讓Server保持跟Client的連線。

socket.io是一個即時通知javascript函式庫，透過server端與client之間建立連線，可以即時的傳送資料給對方，支持雙向通信。可以跨平台做傳輸，在不同平台上傳輸方式的差異性，讓開發者更容易發展即時性的網頁應用程式。

並且可以讓server端主動push event到client端，所有連線可以更即時！

#### 特點

socket.io有幾個特點

1. 支持瀏覽器/Nodejs環境
2. 支持雙向通信
3. API簡單易用
4. 支持二進位傳輸
5. 減少傳輸數據量

socket.io讓使用者可以在處理事件時，能夠快速開發出應用，Socket.io並不是一個基本的、獨立的、能夠回退到其它即時協定的WebSocket庫，它實際上是一個依賴於其它即時傳輸協定的自訂即時傳輸協定的實現。該協定的協商部分使得支援標準WebSocket的客戶端不能直接連接到Socket.io伺服器，並且支援Socket.io的客戶端也不能與非Socket.io框架的WebSocket或Comet伺服器通信。因而，Socket.io要求客戶端與伺服器端均須使用該框架。

socket.io是一個依賴於其它即時傳輸協定，才可以自訂即時傳輸協定，需要用別的傳輸套件，才可以做傳送，一般會看到

#### 應用

##### laravel使用sokcet.io

安裝socket.io client

```bash
npm install --save socket.io-client
```

socket.io連線

```javascript
import Echo from "laravel-echo"

window.io = require('socket.io-client');

window.Echo = new Echo({
    broadcaster: 'socket.io',
    host: window.location.hostname + ':6001'
});
```

laravel本身並沒有兼容socket服務的server，需要安裝laravel-echo-server，把服務架起來

```bash
npm install -g laravel-echo-server
```

安裝完後執行初始化

```bash
laravel-echo-server init
```



這裡socket.io是由laravel-echo-server將推播資料送到前端，在由laravel echo接收資料做解析，要做即時通知的功能，讓開發者可以不用透過browser也可以達到跟socketio server連線的方式。

#### **參考資料**

[socketio的維基百科](https://zh.wikipedia.org/wiki/Socket.IO)

[websocket的維基百科](https://zh.wikipedia.org/wiki/WebSocket)

[使用 Node.js 與 Socket.IO 建立即時性（Realtime）網頁應用程式 App](https://blog.gtwang.org/programming/socket-io-node-js-realtime-app/)

