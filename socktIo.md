#### **socket.io**

- 介紹
- 特點
- 應用

#### 介紹

socket.io是一個即時通知javascript函式庫，透過server端與client之間建立連線，可以即時的傳送資料給對方，支持雙向通信。可以跨平台做傳輸，在不同平台上傳輸方式的差異性，讓開發者更容易發展即時性的網頁應用程式。

SocketIO為實做Web Socket的Server Socket實作品，透過SocketIO可以讓Server保持跟Client的連線，並且可以讓server端主動push event到client端，所有連線可以更即時！

#### 特點

socket.io有幾個特點

1. 支持瀏覽器/Nodejs環境
2. 支持雙向通信
3. API簡單易用
4. 支持二進位傳輸
5. 減少傳輸數據量

socket.io讓使用者可以在處理事件時，能夠快速開發出應用，Socket.io並不是一個基本的、獨立的、能夠回退到其它即時協定的WebSocket庫，它實際上是一個依賴於其它即時傳輸協定的自訂即時傳輸協定的實現。該協定的協商部分使得支援標準WebSocket的客戶端不能直接連接到Socket.io伺服器，並且支援Socket.io的客戶端也不能與非Socket.io框架的WebSocket或Comet伺服器通信。因而，Socket.io要求客戶端與伺服器端均須使用該框架。

socket.io是一個依賴於其它即時傳輸協定，才可以自訂即時傳輸協定，需要用別的

#### 應用

以下範例會使用laravel-echo-server

這裡socket.io是由laravel-echo-server將推播資料送到前端，在由laravel echo接收資料做解析，要做即時通知的功能，讓開發者可以不用透過browser也可以達到跟socketio server連線的方式。

