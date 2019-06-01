---
title: "HeroKu教學：部署網站"
date: "2019-05-12"
author: "HeroKu"
summary: "說明如何使用HeroKu部署網站"
categories: ["Cloud Platform"]
tags: ["HeroKu","Git"]
---

**Heroku** 是多個雲端伺服器當中之一，結合Github是一個專門讓人放置網站的服務，給開發者一個固定的 URL (含有 HTTPS 憑證)，可快速將自己的專案部署上去。

![background_heroku](https://coolgood88142.github.io/images/background_heroku.png)

以下範例，將自己的專案部署在HeroKu網站上

### 1.註冊[HeroKu](<https://id.heroku.com/login>)帳號



### 2.安裝[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#windows)，用cmd執行HeroKu

```
heroku
```



### 3.登入剛剛註冊的HeroKu帳號

```
heroku login
Email: coolgood88142@gmail.com
Password: xxxxxxx
Logged in as coolgood88142@gmail.com #登入成功
```



### 4.安裝[Git](https://git-scm.com/downloads)，安裝好之後用cmd執行Git

```
git
```



### 5.登入HeroKu網站，在Personal新增create new app

![heroku-1](https://coolgood88142.github.io//images/heroku-1.png)



### 6.從HeroKu網站裡的Setting找HeroKu Git URL並複製，代表app的連結

![heroku-2](https://coolgood88142.github.io/images/heroku-2.png)



### 7.用cmd路徑改到自己要放在HeroKu上版的專案

```
cd C:\xampp\htdocs\javascript_work #自己要部署專案的檔案路徑
```



### 8.將專案上版至HeroKu

```
git init
git remote add origin https://git.heroku.com/quiet-crag-79025.git
git add .
git commit -m "my-first-website"
git push origin master
```



### 9.回到剛剛HeroKu網站，點右上角的Open app，這時會開啟Heroku專案的網頁，顯示專案名稱的網址

![heroku-3](https://coolgood88142.github.io/images/heroku-3.png)



![heroku-4](https://coolgood88142.github.io/images/heroku-4.png)



### 問題

如果網站有使用js或css檔案會有無法載入問題，因為缺少了production環境，可以安裝npm來建立環境，heroku在執行npm時，預設執行指令為npm start，從package.json來設定執行，告訴heroku專案底下的檔案要載入。

#### package.json

```
{
  "name": "javascript_work",
  "version": "1.0.0",
  "description": "this is javascript work",
  "main": "node.js",
  "scripts": {
    "start": "node node.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "license": "ISC",
  "dependencies": {
    "body-parser": "^1.13.3",
    "express": "^4.13.2",
    "http": "0.0.0",
    "json": "^9.0.3",
    "mongoose": "^4.1.0",
    "path": "^0.11.14",
    "serve-favicon": "^2.3.0"
  }
}
```

主要執行scripts裡的start，使用node node.js指令，會執行node.js檔案，以及一些套件。

#### node.js

```
const express = require('express');
const app = express(); 
const path = require('path');
var port = process.env.PORT || 3000;

app.use(express.static(path.join(__dirname, '')));
app.get('/', function (req, res) { 
    res.sendfile(__dirname + '/index.html'); 
});

app.listen(port, function() {
	console.log("Listening on Port 3000");
});
```

使用express套件來建立，指令專案路徑與設定阜(port)，建立環境預設使用index.html檔案，以及專案路徑底下的檔案。

官網部份有提供[資料](https://help.heroku.com/PNKE28Z0/why-are-my-css-js-changes-not-showing-up-in-my-node-app)



參考資料:

<https://ithelp.ithome.com.tw/articles/10196129>、

<https://blog.wu-boy.com/2019/02/deploy-golang-app-to-heroku/>、

<https://id.heroku.com/login>、

<https://devcenter.heroku.com/articles/heroku-cli#windows>、

<https://git-scm.com/downloads>、

<https://www.gomaji.com/store/39678?pid=150656&city=7>、

<https://stackoverflow.com/questions/33057674/js-file-not-found-on-heroku>、

<https://github.com/facebook/create-react-app/issues/4132>