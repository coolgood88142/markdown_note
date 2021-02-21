---
title: "JWT"
date: "2021-02-21"
author: "JWT"
summary: "介紹使用JWT"
---

## JWT

JWT全名為JSON Web Token，是以JSON格式資料加密後當作token，記錄server與client端要傳遞的訊息。

JWT的結構要由三個部分組成，分別是header、payload、signature

- **header**：表頭存放類型與加密算法

  以下範例是預設值，alg可以設定不同的加密算法，但是接收方要解密時，必須用相同的加密算法

  ```php
  {
    "typ": "JWT",
    "alg": "HS256"
  }
  ```

- **payload**：內容設定識別身分、發送時間、過期時間、使用時間，以及自行定義要傳的資料。

  例如：我想要帶一個data裡，有個key為page，value為weather

  ```php
  {
    'iss' => 'http://www.helloweba.net', 
    'aud' => 'http://www.helloweba.net', 
    'iat' => $time, 
    'nbf' => $time, 
    'exp' => $time+7200,
    'data' => [ 
        'page' => 'weather',
    ]
  }
  ```

- **signature**：簽證帶的值，就是加密後的一整串編碼。

  例如：我帶的token是這一整串，會發現中間會有點(.)這個符號，會分成三個字串

  ```text
  token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC93d3cuaGVsbG93ZWJhLm5ldCIsImF1ZCI6Imh0dHA6XC9cL3d3dy5oZWxsb3dlYmEubmV0IiwiaWF0IjoxNjEzODgzMTc3LCJuYmYiOjE2MTM4ODMxNzcsImV4cCI6MTYxMzg5MDM3NywiZGF0YSI6eyJwYWdlIjoid2VhdGhlciJ9fQ.5kdd69W_sDlOe0QnOto78d_axfucL5RbA4pK6vWmIOI
  
  //第一個字串 是將header內容使用base64轉成編碼形成的
  eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
      
  //第二個字串 是將payload內容使用base64轉成編碼形成的
  eyJpc3MiOiJodHRwOlwvXC93d3cuaGVsbG93ZWJhLm5ldCIsImF1ZCI6Imh0dHA6XC9cL3d3dy5oZWxsb3dlYmEubmV0IiwiaWF0IjoxNjEzODgzMTc3LCJuYmYiOjE2MTM4ODMxNzcsImV4cCI6MTYxMzg5MDM3NywiZGF0YSI6eyJwYWdlIjoid2VhdGhlciJ9fQ
  
  //第三個字串 是將header和payload，兩個的base64編碼串在一起，在執行base64編碼
  eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC93d3cuaGVsbG93ZWJhLm5ldCIsImF1ZCI6Imh0dHA6XC9cL3d3dy5oZWxsb3dlYmEubmV0IiwiaWF0IjoxNjEzODgzMTc3LCJuYmYiOjE2MTM4ODMxNzcsImV4cCI6MTYxMzg5MDM3NywiZGF0YSI6eyJwYWdlIjoid2VhdGhlciJ9fQ
   => 5kdd69W_sDlOe0QnOto78d_axfucL5RbA4pK6vWmIOI
  ```
  
  

#### 優點：

1. server不需要保存傳統會話信息，沒有跨域傳輸問題，減小伺服器佔存。
2. jwt構成簡單，占用很少的字節，便於傳輸。
3. json格式通用，不同語言之間都可以使用。

![jwt](C:\xampp\htdocs\markdown_note\assets\images\jwt.png)

以下介紹，程式碼說明

```php
public function lssue(){
    $key = '344'; //自己定義key的內容，在加解密的名稱要相同
    $time = time(); //取得當前時間

    $token = [
        'iss' => 'http://www.helloweba.net', //用字串或url來表示識別JWT的發送方
        'aud' => 'http://www.helloweba.net', //用字串或url夾表示識別JWT的接收方
        'iat' => $time, //設定發送時間
        'nbf' => $time, //定義什麼時間，才可以使用這個token
        'exp' => $time+7200, //設定JWT的過期時間，一定要大於發送時間
        'data' => [ 
            'page' => 'weatherChart', //設定token要傳遞的資料
        ]
    ];
    
    //用JWT的encode作加密，變成一串token資訊，在傳到頁面上
    return view('verification', [
        'token' => '/?token=' . (JWT::encode($token, $key)),
    ]);
}
      
```

```php
public function verification(Request $request){
    $key = '344'; //自己定義key的內容，在加解密的名稱要相同
    $jwt = (String)$request->token; //接收到加密的JWT的資料

    try {
        JWT::$leeway = 60;//減少JWT的時間(60秒)，做緩衝
		$decoded = JWT::decode($jwt, $key, ['HS256']); //JWT的header有定義哪個加密算法，預設為HS256，這裡用相同的加密算法
		$arr = (array)$decoded;
		var_dump($arr); //顯示傳過的token資料
	} catch(\Firebase\JWT\SignatureInvalidException $e) {  //token的signature有問題，例如：加密算法，設定錯誤
		echo $e->getMessage();
	}catch(\Firebase\JWT\BeforeValidException $e) {  //在解密時，當前時間並沒有大於nbf設定時間
		echo $e->getMessage();
	}catch(\Firebase\JWT\ExpiredException $e) {  //當前時間已經超過，exp設定的時間，token過期了
		echo $e->getMessage();
	}catch(Exception $e) {  //其他錯誤
		echo $e->getMessage();
	}
}
```

#### 問題：

1. JWT的token過期後，我還能decode嗎?

   不行，當接收方在做執行JWT的decode()時，會先判斷當前時間，是否已經超過token裡的exp設定的時間，超過時會顯示ExpiredException錯誤訊息

   

2. 是否有比JWT更好的實作方式?

   JWT雖然有用json格式記錄類型、加密算法、識別身分等等，資料比較小又方便，但是與session相比，保存在server端，相對較為安全，如果server端或client端的key，被竊取的話，token就會被解密

   



參考資料:

https://blog.csdn.net/cjs5202001/article/details/80228937、

https://kknews.cc/zh-tw/code/ry55lxx.html、

https://5xruby.tw/posts/what-is-jwt/



