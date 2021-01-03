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

以下範例為lterator，跑while迴圈進行驗證，在印出key和value

```php
<?php

class TestData implements \Iterator
{
    private $var = array();

    public function __construct($array)
    {
        if (is_array($array)) {
            $this->var = $array;
        }
    }

    public function rewind()
    {
        reset($this->var);
    }
  
    public function current()
    {
        $var = current($this->var);
        return $var;
    }
  
    public function key() 
    {
        $var = key($this->var);
        return $var;
    }
  
    public function next() 
    {
        $var = next($this->var);
        return $var;
    }
  
    public function valid()
    {
        $key = key($this->var);
        $var = ($key !== NULL && $key !== FALSE);
        return $var;
    }
}

$testArray = ['a' => 0, 'b' => 1, 'c' => 2, 'd' => 3];
$datas = new TestData($testArray);

while ($datas->valid())
{
    $key = $datas->key();
    $value = $datas->current();

     echo 'key:' . $key . ' value: ' . $value . '<br/><br/>';

     $datas->next();
 }

//key:a values:0
//key:b values:1
//key:c values:2
//key:d values:3
```

程式中有個datas變數宣告TestData物件，存放testArray陣列，迴圈每執行一次進行驗證，將目前陣列物件的key和value印出來，在將物件換成下一個，繼續跑迴圈，執行陣列蒐集完物件為止。

Generator 如果沒設定key， Generator 會為 value 產生一個對應整數，並當成是它的 key。

以下範例為Generator，從1-10之間拿到數字後，做yield回傳物件，印出當前物件的key和value。

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

在跑foreach執行yeild就會暫停，下一次執行迴圈才會繼續，這時yeild會回傳物件不是值，每次執行都是占用相同的記憶體，如果是用return，會一直回傳值，每次執行都占用不同的記憶體，記憶體才會消耗很大。

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

