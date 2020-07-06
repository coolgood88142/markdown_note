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

使用者model建立一個自己的手機



```php
$phone = User::find(1)->phone;
```

利用使用者model在使用者資料表中找出主鍵的第一筆資料，這筆資料model中關連到屬於自己的手機



```php
return $this->hasOne('App\Phone', 'foreign_key');
```

回傳一個自己手機，包含外鍵



```php
return $this->hasOne('App\Phone', 'foreign_key', 'local_key');
```

回傳一個自己手機，包含外鍵以及自訂欄位



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

手機model建立關連擁有手機的使用者，包含外鍵



```php
/**
 * Get the user that owns the phone.
 */
public function user()
{
    return $this->belongsTo('App\User', 'foreign_key', 'other_key');
}
```

手機model建立關連擁有手機的使用者，包含外鍵以及其他欄位



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

貼文model建立多個的評論



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

利用貼文model在貼文資料表中找出主鍵的第一筆資料，在title欄位找出foo的第一筆資料。



```php
return $this->hasMany('App\Comment', 'foreign_key');

return $this->hasMany('App\Comment', 'foreign_key', 'local_key');
```

回傳多個評論，包含外鍵以及自訂欄位。



#### 一對多(逆向)

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Post');
    }
}
```

評論model建立關連屬於自己的貼文



```php
$comment = App\Comment::find(1);

echo $comment->post->title;
```

利用評論model在貼評論資料表中找出主鍵的第一筆資料，這筆資料model中關連到屬於自己貼文的標題資料



```php
/**
 * Get the post that owns the comment.
 */
public function post()
{
    return $this->belongsTo('App\Post', 'foreign_key');
}
```

評論model建立關連屬於自己的貼文，包含外鍵



```php
/**
 * Get the post that owns the comment.
 */
public function post()
{
    return $this->belongsTo('App\Post', 'foreign_key', 'other_key');
}
```

評論model建立關連屬於自己的貼文，包含外鍵以及其他欄位



#### 多對多

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * The roles that belong to the user.
     */
    public function roles()
    {
        return $this->belongsToMany('App\Role');
    }
}
```

使用者model建立多個使用者的角色



```php
$user = App\User::find(1);

foreach ($user->roles as $role) {
    //
}
```

利用使用者model在使用者資料表中找出主鍵的第一筆資料，這筆資料model中關連到屬於自己全部的角色



```php
$roles = App\User::find(1)->roles()->orderBy('name')->get();
```

利用使用者mode在使用者資料表中找出主鍵的第一筆資料，找出關聯到屬於自己全部的角色，並且用name欄位做排序。



```php
return $this->belongsToMany('App\Role', 'role_user');
```

回傳多個屬於自己的角色，包含名為role_user的外鍵。



```php
return $this->belongsToMany('App\Role', 'role_user', 'user_id', 'role_id');
```

回傳多個屬於自己的角色，包含名為role_user的外鍵以及user_id、role_id欄位。



```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Role extends Model
{
    /**
     * The users that belong to the role.
     */
    public function users()
    {
        return $this->belongsToMany('App\User');
    }
}
```

角色model建立屬於自己多個使用者



```php
$user = App\User::find(1);

foreach ($user->roles as $role) {
    echo $role->pivot->created_at;
}
```

利用使用者model在使用者資料表中找出主鍵的第一筆資料，這筆資料model中關連到屬於自己全部的角色，並且建立pivot屬性與時間標記，取得資料表



#### 遠層一對多

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Mechanic extends Model
{
    /**
     * Get the car's owner.
     */
    public function carOwner()
    {
        return $this->hasOneThrough('App\Owner', 'App\Car');
    }
}
```

機器model建立一個擁有者、汽車的資料關聯



```php
class Mechanic extends Model
{
    /**
     * Get the car's owner.
     */
    public function carOwner()
    {
        return $this->hasOneThrough(
            'App\Owner',
            'App\Car',
            'mechanic_id', // Foreign key on cars table...
            'car_id', // Foreign key on owners table...
            'id', // Local key on mechanics table...
            'id' // Local key on cars table...
        );
    }
}
```

機器model建立一個擁有者、汽車的資料以及多個欄位的關聯



參考資料:

https://laravel.com/docs/7.x/eloquent-relationships#one-to-many
