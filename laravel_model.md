---
title: "laravel_model"
date: "2020-07-02"
author: "laravel_model"
summary: "解釋laravel_model範例說明"
---





#### 一對一

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Get the phone record associated with the user.
     */
    public function phone()
    {
        return $this->hasOne('App\Phone');
    }
}
```

使用者model建立關連屬於自己的手機



```php
$phone = User::find(1)->phone;
```

利用使用者model在使用者資料表中找出主鍵的第一筆資料，這筆資料model中關連到屬於自己的手機



```php
return $this->hasOne('App\Phone', 'foreign_key');
```

回傳model關連與外鍵的手機資料



```php
return $this->hasOne('App\Phone', 'foreign_key', 'local_key');
```

回傳model關連與外鍵以及自訂的欄位的手機資料



```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Phone extends Model
{
    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\User');
    }
}
```

手機model建立關連擁有手機的使用者



```php
/**
 * Get the user that owns the phone.
 */
public function user()
{
    return $this->belongsTo('App\User', 'foreign_key');
}
```

model建立關連與外鍵的使用者資料



```php
/**
 * Get the user that owns the phone.
 */
public function user()
{
    return $this->belongsTo('App\User', 'foreign_key', 'other_key');
}
```

model建立關連與外鍵以及自訂的欄位的使用者資料



#### 一對多

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    /**
     * Get the comments for the blog post.
     */
    public function comments()
    {
        return $this->hasMany('App\Comment');
    }
}
```

貼文model建立關連屬於自己的評論



```php
$comments = App\Post::find(1)->comments;

foreach ($comments as $comment) {
    //
}
```

利用貼文model在貼文資料表中找出主鍵的第一筆資料，這筆資料model中關連到屬於自己的評論



```php
$comment = App\Post::find(1)->comments()->where('title', 'foo')->first();
```

利用貼文model在貼文資料表中找出主鍵的第一筆資料，在標題欄位找出名為foo的第一筆資料。



```php
return $this->hasMany('App\Comment', 'foreign_key');

return $this->hasMany('App\Comment', 'foreign_key', 'local_key');
```

回傳model關連與外鍵以及自訂的欄位的評論資料





參考資料:

https://laravel.com/docs/7.x/eloquent-relationships#one-to-many
