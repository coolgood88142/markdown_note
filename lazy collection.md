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

一般使用return會用變數取代資料，例如程式跑foreach，每跑一次就 return，會導致變數佔用很大的記憶體。如果用yield代替return ，只回傳物件並不會占用記憶體。

Generator是實作lterator介面，用來包裝foreach來疊代的物件，而yield是回傳Generator的物件，每次執行到yield就會暫停，直到下一次執行才會繼續。

只要在foreach裡有yield的，就算是一個Generator，當Generator跑迴圈時，每次到yield就回傳，這時不會占用記憶體，因此使用yield可以節省記憶體。

Generator 如果沒設定key， Generator 會為 value 產生一個對應整數，並當成是它的 key。

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

以下範例，使用laravel 抓DB資料，執行`all()`和`cursor()`的差異

```php
public function getArticlesAllData(){
    $datas = Articles::all();

    foreach ($datas as $data) {
        echo $data;
    }
}
```

例如：`getArticlesAllData()`直接拿Articles table所有的資料，如果資料有10000筆，變數佔用很大的記憶體，而導致程式time out，網頁顯示錯誤。`all()`是直接拿table全部資料組成一個collection物件。

```php
public function getArticlesCursorData(){
    $datas = Articles::cursor();

    foreach ($datas as $data) {
        echo $data;
    }
}
```

如果改用`cursor()`，會將table全部資料組成一個Lazy collection的物件，這時`getArticlesCursorData()`一樣拿到Articles table所有的資料，但是占用記憶體小很多。

#### yield與return的差異

return在回傳資料時，會占用記憶體，但是yield是回傳Generator的物件，不會占用記憶體，只有在用賦予變數的情形下才會。

以下範例，兩者占用記憶體差異

```php
public function getArticlesDataRam(){
    $this->getReturnRamText()
    echo '<bt/></br>';
    $this->getYieldRamText();
}

public function getArticles($beginId, $endId){
    $client = new \GuzzleHttp\Client();
    $url = 'https://my-json-server.typicode.com/coolgood88142/json_server/articles?_start='. $beginId . '&_end=' . $endId;
    $response = $client->request('GET', $url);
    $datas = json_decode($response->getBody(), true);

    return $datas;
}

public function getPrintText($data){
    var_dump($data);
    echo '<br/>';
}

public function getYieldRamText(){
    $datas = LazyCollection::make(function() {   
        $beginId = 1;
        $endId = 4;
        $articles = $this->getArticles($beginId-1, $endId);
        
        foreach ($articles as $article){ 
            yield $article;
        }
    });
    
    foreach ($datas as $data){
        $this->getPrintText($data);
    }

    echo memory_get_usage() . 'Bytes' . '<br/>';
}

public function getReturnRamText(){
    $beginId = 1;
    $endId = 4;
    $datas = $this->getArticles($beginId-1, $endId);
    
    foreach ($datas as $data){
        $this->getPrintText($data);
    }
    
    echo memory_get_usage() . 'Bytes' . '<br/>';
}
```


```
array(4) { ["id"]=> int(1) ["title"]=> string(8) "Bhutanan" ["content"]=> string(90) "We do not believe in Gross National Product. Gross National Happiness is more important..." ["slug"]=> string(9) "buthananc" }
array(4) { ["id"]=> int(2) ["title"]=> string(10) "Kazakhstan" ["content"]=> string(149) "The national drink is made from fermented horse milk (not urine as Borat would have you believe!); one of Kazakhstan’s most popular dishes, Kazy..." ["slug"]=> string(10) "kazakhstan" }
array(4) { ["id"]=> int(3) ["title"]=> string(11) "North Korea" ["content"]=> string(128) "A proudly reclusive, quasi-communist state, no list of the world’s weirdest countries would be complete without North Korea..." ["slug"]=> string(11) "north-korea" }
array(4) { ["id"]=> int(4) ["title"]=> string(8) "Belarusv" ["content"]=> string(163) "After the fall of the Soviet Union, a cluster of countries in Eastern Europe pushed to join the EU. Enticed by grand promises from the continent’s big players..." ["slug"]=> string(8) "belarusv" }
占用了13386760Bytes

array(4) { ["id"]=> int(1) ["title"]=> string(8) "Bhutanan" ["content"]=> string(90) "We do not believe in Gross National Product. Gross National Happiness is more important..." ["slug"]=> string(9) "buthananc" }
array(4) { ["id"]=> int(2) ["title"]=> string(10) "Kazakhstan" ["content"]=> string(149) "The national drink is made from fermented horse milk (not urine as Borat would have you believe!); one of Kazakhstan’s most popular dishes, Kazy..." ["slug"]=> string(10) "kazakhstan" }
array(4) { ["id"]=> int(3) ["title"]=> string(11) "North Korea" ["content"]=> string(128) "A proudly reclusive, quasi-communist state, no list of the world’s weirdest countries would be complete without North Korea..." ["slug"]=> string(11) "north-korea" }
array(4) { ["id"]=> int(4) ["title"]=> string(8) "Belarusv" ["content"]=> string(163) "After the fall of the Soviet Union, a cluster of countries in Eastern Europe pushed to join the EU. Enticed by grand promises from the continent’s big players..." ["slug"]=> string(8) "belarusv" }
占用了13502504Bytes
```

使用`getArticlesDataRam()`執行`getReturnRamText()`和`getYieldRamText()`，用Guzzle套件讀取api的資料，在用foreach去拿到每筆資料，在用var_dump印出來。

`getReturnRamText()`是直接拿資料後印出來，`getYieldRamText()`是用Lazy Collection拿建立Generator，在跑foreach時，每次到yield就會停下，重複做回傳Generator物件，所以並不會一直將記憶體累加。



參考資料：https://ithelp.ithome.com.tw/articles/10194457、

https://segmentfault.com/a/1190000018457194、

https://guzzle-cn.readthedocs.io/zh_CN/latest/quickstart.html

