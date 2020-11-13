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
           DB::collection('admins')->insert([     
   			'username' => 'fadmin',
               'email' => 'admin@fblog.com',
               'email_verification_date' => '',
               'password' => bcrypt('password')
           ]);
   ```

2. ### 登入

   登入的route為http://127.0.0.1:8000/login，畫面上要輸入email與密碼，輸入完之後，`VALIDATE`是對帳密進行驗證，`RESET FORM`是對清除帳密的欄位

   ![fblog1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog1.png>)

   登入成功後，顯示以下畫面，若登入失敗，在console.log顯示401錯誤代碼

   ![fblog2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog2.png>)

3. ### 新增文章

   按下NEW ARTICLE，會顯示編輯文章的視窗，視窗中輸入標題(title)、內容(content)、文章連結(URL)

   ![fblog3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog3.png>)

   新增成功後，就會顯示多一筆

   ![fblog4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog4.png>)

   這時可以輸入`我的第一篇文章`網址，http://127.0.0.1:8000/article/myfirst#/，就會顯示剛剛新增的文章內容

   ![fblog5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog5.png>)

4. ### 編輯文章

   點選編輯圖示，跳出編輯視窗

   ![fblog6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog6.png>)

   ![fblog7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog7.png>)

   修改完文章後按save(儲存)，並且重新整理畫面

   ![fblog8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog8.png>)

5. ### 刪除文章

   點選刪除圖示

   ![fblog9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog9.png>)

   顯示是否要刪除文章?(Are you sure you want to delete this article?)，確定之後直接移除文章

   ![fblog10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog10.png>)

   ![fblog11](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog11.png>)

6. ### 切換背景色

   關閉The dark side，會顯示背景白色

   ![fblog12](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog12.png>)

#### 程式流程

![fblog13](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog13.png>)

![fblog14](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog14.png>)

![fblog15](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog15.png>)

![fblog16](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog16.png>)

![fblog17](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog17.png>)

### 循序圖

![fblog18](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog18.png>)

![fblog19](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog19.png>)

![fblog20](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog20.png>)

![fblog21](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog21.png>)

![fblog22](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fblog22.png>)

依照route.js分別執行顯示component

```javascript
import VueRouter from 'vue-router';

// Components
import ArticleList from '@/js/components/ArticleList';
import Article from '@/js/components/Article';
import Login from '@/js/components/Login';
import ArticleAdminList from '@/js/components/admin/ArticleAdminList';

const router = new VueRouter({
    history: true,
    mode: 'history',
    routes: [{
        path: '/',
        name: 'articles',
        component: ArticleList,
        meta: {
            auth: undefined
        }
    }, {
        path: '/article/:slug',
        name: 'article',
        component: Article,
        meta: {
            auth: undefined
        }
    }, {
        path: '/login',
        name: 'adminLogin',
        component: Login,
        meta: {
            auth: undefined
        }
    }, {
        path: '/admin',
        name: 'admin',
        component: ArticleAdminList,
        meta: {
            auth: true,
            redirect: {
                name: 'adminLogin'
            },
            forbiddenRedirect: '/403'
        }
    }]
});

export default router;

```

##### 首頁(http://127.0.0.1:8000)

所有的Route全部指定app.blade.php，透過vue-router對應的route中，指向是先設定好的components，首頁的Route會先指向`ArticleList.vue`顯示全部的文章，這時跑後端會執行`ArticleController`的`index()`，拿到所有文章資料，每筆文章有個`Continue reading...`按鈕，監聽`showArticle(slug)`，參數帶文章的`URL`，`showArticle`是顯示文章的標題、文章內容。

##### 文章頁面(http://127.0.0.1:8000/article/:slug)

在`ArticleList.vue`點選文章後，執行`showArticle(slug)`，在vue-router對應`article/:slug`指向`Article.vue`，會依照`slug`這個參數去找文章，執行`ArticleController`的`show()`，顯示某個文章資料，並且顯示在頁面上。

##### 登入頁面(http://127.0.0.1:8000/login)

在最外層的`App.vue`點選登出圖示，執行`logout()`，在vue-router對應`/login`指向`Login.vue`，輸入帳號密碼後，點選`Validate`按鈕，跑到後端`AuthController`的`Login()`做驗證，若是回傳`success`會執行`router`的`admin`，`error`則是會在`console.log`顯示`401`錯誤訊息，如果是點選`Reset Form`按鈕會清空，剛輸入的帳號密碼資料。

##### 登入成功頁面(http://127.0.0.1:8000/admin)

登入成功會在vue-router對應`/admin`指向`ArticleAdminList.vue`，畫面載入時到`created()`執行`initialize()`，vue-router對應`api/articles`指向，執行`ArticleController`的`index()`，在`mongodb`拿到所有文章資料。

點選`NEW ARTICLE`按鈕，會先執行`computed`的`formTitle()`決定視窗的標題文字，顯示編輯視窗輸入完標題(title)、內容(content)、文章連結(URL)，按下save按鈕，執行`ArticleController`的`store()`會將資料存到`mongodb`。

點選某個文章的編輯圖示，可以編輯文章資料，會先執行`computed`的`formTitle()`，再執行`editItem()`，將資料傳到編輯視窗，修改文章內容後，按下save按鈕，執行`ArticleController`的`update()`會將資料存到`mongodb`。

點選某個文章的刪除圖示，可以將文章移除掉，執行`deleteItem()`，會顯示訊息`Are you sure you want to delete this article?`，執行`ArticleController`的`destroy()`，在`mongodb`做移除文章資料，再更新現有資料。

#### f-blog的creation_date與keyword的created_at列出差異

```
"creation_date" : {
        "date" : "2020-10-20 18:34:34.490048",
        "timezone_type" : 3,
        "timezone" : "UTC"
},

"created_at" : "2020-10-02 20:25:08"
```

creation_date的時間格式是直接儲存Date物件，而不是組的，ex:直接儲存`Carbon::now()`

created_at是儲存`Carbon::now()->toDateTimeString()`，是將Date物件轉成String

參考資料：https://github.com/dchaur/f-blog#testing-the-api