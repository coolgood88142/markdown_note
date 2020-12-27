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

一般使用return會用變數取代資料，例如程式跑foreach，每跑一次就 return，會導致變數佔用很大的記憶體。我們可以用yield代替return ，來節省記憶體。

Generator跟lterator很像，lterator是集合物件跑foreach時，做疊代的動作，Generator有多一個yield，每次執行到yield就會暫停，直到下一次執行才會繼續，重複做這個動作直到迴圈結束。

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

return在回傳資料時，會將資料存取到變數，但是yield是回傳Generator的物件，將資料做疊代，並且直到迴圈跑完才存取到變數，這兩者看起return每做一次就存取，會占用很大的記憶體，而yield是迴圈跑完後才存取，相對之占用記憶體較小。

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

public function getYieldRamText(){
    $datas = LazyCollection::make(function() {   
        $beginId = 1;
        $endId = 4;
        $articles = $this->getArticles($beginId-1, $endId);
        
        foreach ($articles as $article){ 
            yield $article;
        }
    });
    
    $sum = array();
    foreach ($datas as $data){
        array_push($sum, $data);
    }

    echo 'yield占用了' . memory_get_usage() . 'Bytes' . '<br/>';
}

public function getReturnRamText(){
    $beginId = 1;
    $endId = 4;
    $datas = $this->getArticles($beginId-1, $endId);
    
    $sum = array();
    foreach ($datas as $data){
        array_push($sum, $data);
    }
    
    echo 'return占用了' . memory_get_usage() . 'Bytes' . '<br/>';
}
```


```
return占用了13387416Bytes

yield占用了13505392Bytes
```

使用`getArticlesDataRam()`執行`getReturnRamText()`和`getYieldRamText()`，用Guzzle套件讀取api的資料，在用`foreach`存取變數占用記憶體，在用`memory_get_usage()`印出當前的記憶體。

`getReturnRamText()`是直接拿資料後印出來，每跑一次迴圈就存取到變數，占用記憶體會很大，`getYieldRamText()`是用`Lazy Collection`跑`foreach`時，每次到yield就會停下，重複做回傳Generator物件，直到迴圈結束後，只存取一次到變數，所以占用記憶體會比較小。



參考資料：https://ithelp.ithome.com.tw/articles/10194457、

https://segmentfault.com/a/1190000018457194、

https://guzzle-cn.readthedocs.io/zh_CN/latest/quickstart.html

