## HeroKu：部署網站

1.註冊HeroKu帳號

網址:<https://www.heroku.com/>

2.建立app輸入完名稱，這個就是我們要部署的專案

3.安裝 Heroku 工具，套過上方的網址可下載

4.用cmd執行heroku login，把剛剛註冊的帳號做登入

5.安裝git

6.裝完後再想要部署的資料夾裡，用cmd切換到路徑後執行git init

7.剛剛heroku建立的app，有heroku git URL，遠端上版的路徑

8.執行後git init會初始化，在執行git remote add heroku "heroku git URL" 

9.以下使用npm來執行編譯並上版到HeroKu





npm install 產生node_modules
npm init 初始化設定完 產生package.json
npm 套件名稱 install package.json就產生套件的版本裝在開發還是線上
在修改webpack.config.js內容後，將自己寫的js有使用套件的部分，全部打包成一個js檔案
在package.json裡的 scripts新增腳本名稱，例如:build執行webpack，執行指令npm run build
就可以將執行webpack打包



參考資料:[https://medium.com/@chs_wei/%E5%9C%A8-github-%E9%83%A8%E7%BD%B2-hugo-%E9%9D%9C%E6%85%8B%E7%B6%B2%E7%AB%99-9c40682dfe40](https://medium.com/@chs_wei/在-github-部署-hugo-靜態網站-9c40682dfe40)