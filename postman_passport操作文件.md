# postman laravel passport

## 大綱

- 簡介
- AuthorizationCode
- AccessToken
- UserInfo

### 簡介

用laravel passport開發第三方登入，用OAuth2 的Authorization Code模式，在本機開發兩個站台，一個laravel做登入，另一個做第三方登入

以下介紹使用postman 取得AuthorizationCode、AccessToken、UserInfo

### AuthorizationCode

在跑AuthorizationCode之前，要從登入再登入取得session

![postmanPassport1.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport1.png)

拿到跑AuthorizationCode，補上client_id、redirect_uri、response_type、scope、state，再把session放到Authorization的Value裡，前面還要加上Bearer，要在空一格，放到後面

![postmanPassport2.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport2.png)

成功之後，會得到AuthorizationCode

![postmanPassport3.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport3.png)

### AccessToken

輸入參數grant_type、redirect_uri、code、client_id、client_secret

![postmanPassport5.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport5.png)

成功之後，會拿到AccessToken

![postmanPassport6.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport6.png)

### UserInfo

postman可以跑OAuth 2.0，直接拿到AccessToken，在Authorization的Type選擇OAuth 2.0，按create New Access Token按鈕

![postmanPassport7.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport7.png)

輸入帳密後點選登入按鈕

![postmanPassport8.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport8.png)

等幾秒鐘，系統會給Access Token，點選右上角的Use Token，系統就會把Access Token丟到參數裡

![postmanPassport9.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport9.png)

![postmanPassport10.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport10.png)

之後輸入完發送的網址再發送，就會拿到user資料

![postmanPassport11.png](C:\xampp\htdocs\markdown_note\assets\images\postmanPassport11.png)

### 參考資料

- https://laravel.com/docs/6.x/passport
- https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E8%AA%8D%E8%AD%98-oauth-2-0-%E4%B8%80%E6%AC%A1%E4%BA%86%E8%A7%A3%E5%90%84%E8%A7%92%E8%89%B2-%E5%90%84%E9%A1%9E%E5%9E%8B%E6%B5%81%E7%A8%8B%E7%9A%84%E5%B7%AE%E7%95%B0-c42da83a6015
- https://docs.vmware.com/tw/VMware-Cloud-services/services/Using-VMware-Cloud-Services/GUID-53D39337-D93A-4B84-BD18-DDF43C21479A.html
- https://ithelp.ithome.com.tw/articles/10270271
