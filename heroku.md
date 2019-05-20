## HeroKu：部署網站

**Heroku** 是多個雲端伺服器當中之一，結合Github是一個專門讓人放置網站的服務，給開發者一個固定的 URL (含有 HTTPS 憑證)，可快速將自己的專案部署上去。

![background_heroku](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/background_heroku.png)

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

![heroku-1](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/heroku-1.png)



### 6.從HeroKu網站裡的Setting找HeroKu Git URL並複製，代表app的連結

![heroku-2](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/heroku-2.png)



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

![heroku-3](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/heroku-3.png)



![heroku-4](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/heroku-4.png)



參考資料:<https://ithelp.ithome.com.tw/articles/10196129>、<https://blog.wu-boy.com/2019/02/deploy-golang-app-to-heroku/>、<https://id.heroku.com/login>、<https://devcenter.heroku.com/articles/heroku-cli#windows>、<https://git-scm.com/downloads>、<https://www.gomaji.com/store/39678?pid=150656&city=7>