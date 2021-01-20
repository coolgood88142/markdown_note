---
title: "Line Notify"
date: "2020-05-20"
author: "Line Notify"
summary: "介紹使用氣象API，將資料顯示在畫面上"
---

## 氣象API

是中央氣象局提供的開放式資料，我們可以利用API快速拿到氣象局的最新資訊。

#### 1.新增專案

#### 安裝npm

從[node.js官網](https://nodejs.org/en/download/)下載下來，在安裝node.js過程中就有包含npm，安裝完之後執行版本指令，確認是否安裝成功

```
npm -v
6.12.1
```

#### 安裝composer

進入[下載網址](https://getcomposer.org/download/)，點擊Composer-Setup.exe，下載完之後安裝起來，執行版本指令，確認是否安裝成功

```
composer --version
Composer version 1.9.0 2019-08-02 20:55:32
```

#### 安裝laravel

```
composer create-project --prefer-dist laravel/laravel weather_fun 6.*
```

#### 安裝vue 

```
npm install --save-dev vue@2.6.12
```

#### 2.設定Vue全域變數

在專案中的resources/js/app.js檔案，新增變數`Vue`

```
window.Vue = require('vue');
```



#### 新增排程

**Heroku** 有個 **Heroku Scheduler**可以設定某個時間點，執行什麼指令

新增job之後，設定指令

```
php artisan schedule:run
```



### 問題

拿掉的那段程式碼，是一個function整用括號包起來，我發現dev編譯會照寫，prod會用變數命名變成陣列，才會壞掉



參考資料:

https://chibupapa.com/2019/11/12/line-notify/、

https://notify-bot.line.me/zh_TW/、

https://developers.google.com/apps-script/reference/url-fetch/url-fetch-app



