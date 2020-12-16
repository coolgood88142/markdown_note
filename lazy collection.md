---
title: "lazy collection"
date: "2020-12-13"
author: "lazy collection"
summary: "介紹lazy collection"
categories: ["lazy collection"]
tags: ["lazy collection"]
---

lazy collection是laravel用原生PHP的yield、Generator，組成的library。

#### 什麼是yield、Generator?

一般使用return會用變數取代資料，例如程式跑foreach，每跑一次就 return，會導致一個變數，佔用很大的記憶體。如果用yield代替return ，在同一個記憶體空間做疊代。

yield是回傳一個Generator的物件，以lterator接口

yield的好處就是節省記憶體，當generator()執行到yield，會直接使用的當前已佔用的記憶體。

Generator是一個生成器物件，在裡面用yield，每次執行時只回傳一個yield的資料，持續到下次執行才會傳下一個yield的資料

在function 中Generator，用foreach每跑一次，就會停止並且回傳當前的value，每一次的疊代會執行yield那行為止。

//要怎麼利用yield和Generator達到節省記憶體，因為在foreach的Generator，如果用變數賦予就沒意義了

//foreach做return是回傳資料，這時又佔用記憶體，如果是yield就不會占用記憶體而是一個Generator物件
到這個時候

Generator 如果沒設定key， Generator 會為 value 產生一個對應整數，並當成是它的 key

```php
<?php

function generator()
{
    foreach(range(1, 10) as $val) {
        yield ('test' . $val) => $val;
    }
}

foreach (generator() as $key => $val) {
    echo "{$key}: {$val}" . PHP_EOL;
}

// output:
// test1: 1
// test2: 2
// test3: 3
// test4: 4
// test5: 5
// test6: 6
// test7: 7
// test8: 8
// test9: 9
// test10: 10
```

#### lazy collection的用法

以下範例使用laravel 抓DB資料

```php
public function test1(){
    $datas = Articles::all();

    foreach ($datas as $data) {
        echo $data;
    }
}
```

例如：`test1()`直接拿Keyword table所有的資料，如果資料有10000筆，變數佔用很大的記憶體，而導致程式`time out`，網頁顯示錯誤。`all()`是直接拿table全部資料組成一個collection物件。

```php
public function test2(){
    $datas = Articles::cursor();

    foreach ($datas as $data) {
        echo $data;
    }
}
```

如果改用cursor()，會將table全部資料組成一個lazy collection的物件，這時`test2()`一樣拿到Keyword table所有的資料，但是記憶體小很多。

兩種寫法一樣都是印出articles的所有資料。

```php
public function test3(){
    $articles = LazyCollection ::make(function() {
        $client = new \GuzzleHttp\Client();
        $response = $client->request('GET', 'https://my-json-server.typicode.com/coolgood88142/json_server/articles');
        $datas = json_decode($response->getBody(), true);
            
     	foreach ($datas as $data) {
            yield $data;
        }
    });

    foreach ($articles as $data) {
        var_dump($data);
    }
}
```

以上範例，使用call api response用LazyCollection，執行yield，並且顯示資料。

用Guzzle套件讀取api的資料，在用foreach去拿到每筆資料執行yield，在用var_dump印出來



參考資料：https://ithelp.ithome.com.tw/articles/10194457、

https://segmentfault.com/a/1190000018457194、

https://guzzle-cn.readthedocs.io/zh_CN/latest/quickstart.html

