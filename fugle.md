---
title: "fugle"
date: "2021-02-04"
author: "fugle"
summary: "介紹使用富果API結合Line Bot作股票資訊"
---

## Fugle API

[Fugle Realtime API ](https://developer.fugle.tw/)是由 [時報資訊](https://info.infotimes.com.tw/) 與 Fugle 富果技術團隊共同開發，提供台股及時行情 API。

還需要申辦玉山證券富果帳戶，才可以免費申請  API token 使用 Fugle 富果 API。

在申辦玉山證券富果帳戶，必須要有玉山銀行帳戶，開戶還需要7天作業時間。

申辦成功後可以拿API token 進行下一步

![fugle](C:\xampp\htdocs\markdown_note\assets\images\fugle.PNG)



#### 1.富果API有哪些股票資訊?





#### 問題紀錄：

1. 使用ngrok在本機測試，系統一直顯示419 unknown status，用log找錯誤訊息也沒有顯示?

   要在app/http/Middleware/VerifyCsrfToken.php，這隻檔案的except新增`/webhook`，告訴laravel這個路徑不需要驗證。

   ```php
   protected $except = [
   	'/webhook'
   ];
   ```

   這個問題卡了我兩天，老家在宜蘭，沒搞定都回不去了QQ

2. Line Bot 組文字資料使用換行符號(/n)，怎麼沒反應

   在PHP已經都習慣寫單引號，要換改用雙引號才有用。
   
   

參考資料:

https://stackoverflow.com/questions/46266553/why-does-the-laravel-api-return-a-419-status-code-on-post-and-put-methods、

https://eric0324.github.io/2019/09/16/let-line-chatbot-say-hello-world/、

https://developers.line.biz/en/



