---
title: "laravel_mongodb"
date: "2020-12-10"
author: "Mongodb"
summary: "說明使用laravel建立新專案，並且使用mongodb"
categories: ["Mongodb"]
tags: ["Mongodb"]
---

### laravel_mongodb

#### 1.建立新專案

建立專案名稱為laravel_fblog

```

```

#### 2.安裝mongodb套件

```jsx
composer require jenssegers/mongodb ^3.6
```

安裝成功之後，在laravel的config/app.php的providers，新增這行

```jsx
Jenssegers\\Mongodb\\MongodbServiceProvider::class,
```

#### 3.設定mongodb連線

```
//env
DB_CONNECTION=mongodb
DB_HOST=127.0.0.1
DB_PORT=27017
DB_DATABASE=mydb
DB_USERNAME=
DB_PASSWORD=
```

```
//database
'connections' => [
  'mongodb' => [
		'driver' => 'mongodb',
		'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', 27017),
    'database' => env('DB_DATABASE', 'mydb'),
    'username' => env('DB_USERNAME', ''),
    'password' => env('DB_PASSWORD', ''),
    'options' => [
			'database' => env('DB_AUTHENTICATION_DATABASE', 'admin'),
    ],
  ],
]
```

#### 4.建立migration

建立articles資料表

```
php artisan make:migration create_articles_table
```

#### 5.建立seeder

```
php artisan make:seeder ArticlesTableSeeder
```

修改ArticlesTableSeeder

```
<?php

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class ArticlesTableSeeder extends Seeder
{
    /**
     *  Adding dummy articles
     */
    public function run()
    {
        DB::collection('articles')->delete();

        DB::collection('articles')->insert([
            'title' => 'Bhutan',
            'content' => '“We do not believe in Gross National Product. Gross National Happiness is more important”. And so the teenage King of Bhutan famously said in 1979. Since then, Gross National Happiness has become a Bhutanese philosophy.',
            'slug' => 'buthan',
            'creation_date' => new DateTime('2 days')
        ]);

        DB::collection('articles')->insert([
            'title' => 'Kazakhstan',
            'content' => 'The national drink is made from fermented horse milk (not urine as Borat would have you believe!); one of Kazakhstan’s most popular dishes, Kazy, is smoked horsemeat sausage; and the country’s most popular sport, “Buzkashi”, which literally means “grabbing the dead goat”, sees players on horseback vie for control of the “ball”, which, as the name suggests, is the headless carcass of a goat.',
            'slug' => 'kazakhstan',
            'creation_date' => new DateTime('3 days')
        ]);

        DB::collection('articles')->insert([
            'title' => 'North Korea',
            'content' => 'A proudly reclusive, quasi-communist state, no list of the world’s weirdest countries would be complete without North Korea. While Beijing is the only way for Western tourists to enter the country, once inside, it’s surprisingly safe, provided you toe the government’s line. You’ll be accompanied everywhere by two state-employed guides, and hear a somewhat questionable account of North Korea’s history. You’ll be under constant surveillance during your stay, and probably only see what the government wants you to see. Unsettling, yes, but it is a small preview of what life is like in the world’s most tightly controlled nation. Visas to North Korea are issued in Beijing, so this is where you should shop around for tours. Koryo Tours has been touring North Korea for 20 years, while Young Pioneer Tours is the go-to group for younger travellers.',
            'slug' => 'north-korea',
            'creation_date' => new DateTime('20 days')
        ]);

        DB::collection('articles')->insert([
            'title' => 'Belarus',
            'content' => 'After the fall of the Soviet Union, a cluster of countries in Eastern Europe pushed to join the EU. Enticed by grand promises from the continent’s big players, Belarus’s neighbours quickly signed up. But Belarus, nestled between Russia and Poland, bucked the trend and opted for isolation and reclusiveness, essentially turning its back on a united Europe. Off the radar of most travellers, a trip to Belarus offers a much-needed respite from the crowds of Europe’s more popular cities. Its capital, Minsk, has been burned down 18 times.',
            'slug' => 'belarus',
            'creation_date' => new DateTime('11 days')
        ]);
    }
}

```

#### 6.新增Models

```
php artisan make:model Models/Articles
```

修改Articles.php

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Articles extends Model
{
    protected $collection = 'articles';
    public $timestamps = false;
}

```

#### 7.新增CURD

#### insert(新增資料)

laravel的做法是用save儲存資料

```php
public function addArticlesData(Request $request)
{
    $title = $request->title;
    $content = $request->content;
    $slug = $request->slug;
    $status = 'success';
    $message = '新增成功!';

    try {
        $articles = new Articles();
        $articles->title = $title;
        $articles->content = $content;
        $articles->slug = $slug;
        $articles->save();
     } catch (Exception $e) {
        echo $e;
     }
        
     return [
         'status' => $status,
         'message' => $message 
    ];
}
```



#### update(更新資料)

laravel的做法是用save更新資料

```php
public function updateArticlesData(Request $request)
{
    $title = $request->title;
    $content = $request->content;
    $slug = $request->slug;
    $status = 'success';
    $message = '新增成功!';

    try {
        $check = $this->checkAticlesData($id);

        if ($check) {
            $articles = Articles::find($id);
            $articles->title = $title;
            $articles->content = $content;
            $articles->slug = $slug;
            $articles->save();
        } else {
            $status = 'error';
            $message = '更新失敗!';
        }
            
    } catch (Exception $e) {
        echo $e;
    }
        
    return [
        'status' => $status,
            'message' => $message 
    ];
}
```



#### delete(刪除資料)

laravel的做法是用delete刪除資料

```php
public function deleteArticlesData(Request $request)
{
    $id = $request->id;
    $status = 'success';
    $message = '刪除成功!';

    try {
        $check = $this->checkAticlesData($id);

        if ($check) {
            $articles = Aticles::find($id);
            $articles->delete();
        } else {
            $status = 'error';
            $message = '刪除失敗!';
        }

    } catch (Exception $e) {
        echo $e;
    }
        
    return [
        'status' => $status,
        'message' => $message
    ];
}

```

