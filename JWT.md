---
title: "JWT"
date: "2021-02-21"
author: "JWT"
summary: "介紹使用JWT"
---

## JWT

JWT全名為JSON Web Token，是以JSON格式資料加密後當作token，記錄server與client端要傳遞的訊息。

優點：

1. server不需要保存傳統會話信息，沒有跨域傳輸問題，減小伺服器開銷。
2. jwt構成簡單，占用很少的字節，便於傳輸。
3. json格式通用，不同語言之間都可以使用。

- **授權(Authorization)**：例如使用者從 Client 端登入後，該使用者再次對 Server 端發送請求的時候，會夾帶著 JWT，允許使用者存取該 token 有權限的資源。單一登錄(Single Sign On)是當今廣泛使用 JWT 的功能之一，因為它的成本較小並且可以在不同的網域(domain)中輕鬆使用。
- **訊息交換(Information Exchange)**：JWT 可以透過公鑰/私鑰來做簽章，讓我們可以知道是誰發送這個 JWT，此外，由於簽章是使用 header 和 payload 計算的，因此還可以驗證內容是否遭到篡改。



以下介紹，程式碼說明

```php
public function lssue(){
    $key = '344'; //自己定義key的內容，在加解密的名稱要相同
    $time = time(); //取得當前時間

    $token = [
        'iss' => 'http://www.helloweba.net', //用字串或url來表示識別JWT的發文方
        'aud' => 'http://www.helloweba.net', //用字串或url夾帶JWT的唯一訊息
        'iat' => $time, //設定發送時間
        'nbf' => $time, //定義什麼時間，才可以使用這個token
        'exp' => $time+7200, //設定JWT的過期時間，一定要大於發送時間
        'data' => [ 
            'page' => 'weatherChart', //設定token要傳遞的資料
        ]
    ];
}
      
```

```php
public function verification(Request $request){
    $key = '344'; //自己定義key的內容，在加解密的名稱要相同
    $time = time(); //取得當前時間

    $token = [
        'iss' => 'http://www.helloweba.net', //用字串或url來表示識別JWT的發文方
        'aud' => 'http://www.helloweba.net', //用字串或url夾帶JWT的唯一訊息
        'iat' => $time, //設定發送時間
        'nbf' => $time, //定義什麼時間，才可以使用這個token
        'exp' => $time+7200, //設定JWT的過期時間，一定要大於發送時間
        'data' => [ 
            'page' => 'weatherChart', //設定token要傳遞的資料
        ]
    ];
}
```



參考資料:

https://blog.csdn.net/cjs5202001/article/details/80228937、

https://kknews.cc/zh-tw/code/ry55lxx.html、

https://5xruby.tw/posts/what-is-jwt/



