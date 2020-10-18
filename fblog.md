---
title: "fblog"
date: "2020-10-19"
author: "fblog"
summary: "說明fblog範例，怎麼操作文件"
categories: ["Mongodb"]
tags: ["fblog"]
---

## F-Blog

以下介紹F-Blog的範例操作

1. ### 帳密

   mongodb有個admins的collection新增帳密資料，欄位分別是`使用者名稱`、`email`、`email驗證日期`、`密碼`

   密碼這裡使用到bcrypt，這個是將密碼做加密，在collection顯示加密後的資料

   ```php
               'username' => 'fadmin',
               'email' => 'admin@fblog.com',
               'email_verification_date' => '',
               'password' => bcrypt('password')
           ]);
   ```

2. ### 登入

   登入的route為http://127.0.0.1:8000/login，畫面上要輸入email與密碼，輸入完之後，`VALIDATE`是對帳密進行驗證，`RESET FORM`是對清除帳密的欄位

   ![fblog1](https://coolgood88142.github.io/images/fblog1.png)

   登入成功後，顯示以下畫面，若登入失敗，在console.log顯示401錯誤代碼

   ![fblog2](https://coolgood88142.github.io/images/fblog2.png)

3. ### 新增文章

   按下NEW ARTICLE，會顯示編輯文章的視窗，視窗中輸入標題(title)、內容(content)、文章連結(URL)

   ![fblog3](https://coolgood88142.github.io/images/fblog3.png)

   新增成功後，就會顯示多一筆

   ![fblog4](https://coolgood88142.github.io/images/fblog4.png)

   這時可以輸入`我的第一篇文章`網址，http://127.0.0.1:8000/article/myfirst#/，就會顯示剛剛新增的文章內容

   ![fblog5](https://coolgood88142.github.io/images/fblog5.png)

4. ### 編輯文章

   點選編輯圖示，跳出編輯視窗

   ![fblog6](https://coolgood88142.github.io/images/fblog6.png)

   ![fblog7](https://coolgood88142.github.io/images/fblog7.png)

   修改完文章後按save(儲存)，並且重新整理畫面

   ![fblog8](https://coolgood88142.github.io/images/fblog8.png)

5. ### 刪除文章

   點選刪除圖示

   ![fblog9](https://coolgood88142.github.io/images/blog9.png)

   顯示是否要刪除文章?(Are you sure you want to delete this article?)，確定之後直接移除文章

   ![fblog10](https://coolgood88142.github.io/images/fblog10.png)

   ![fblog11](https://coolgood88142.github.io/images/fblog11.png)

6. ### 切換背景色

   關閉The dark side，會顯示背景白色

   ![fblog12](https://coolgood88142.github.io/images/fblog12.png)



參考資料：https://github.com/dchaur/f-blog#testing-the-api