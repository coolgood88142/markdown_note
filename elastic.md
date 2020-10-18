---
title: "ELK Log"
date: "2020-08-02"
author: "Elastic"
summary: "說明如何使用Elastic建立Log"
categories: ["Log"]
tags: ["Elastic","Log"]
---

**ELK** 是由Elasticsearch、Logstash與Kibana三個系統組成的，主要是蒐集Log、分析、查詢。

![elastic](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic.png>)



#### Elasticsearch

是一個分散式搜尋引擎，可以做搜集、分析、存取數據，有效的從大量的資料中，快速找到相關資訊。

#### Logstash

主要是收集日誌的主機上，server端負責將收到的各節點日誌進行過濾、修改等操作在一併發往elasticsearch上去。

#### Kibana

是做為Elasticsearch、Logstash的Web界面，匯整數據資料。



## Windows安裝ELK

以下介紹，windows安裝ELK

### 1.下載Elasticsearch

https://www.elastic.co/downloads/elasticsearch

下載完後並解壓縮



### 2.啟動Elasticsearch

在bin的資料夾裡，執行elasticsearch.bat，看到127.0.0.1:9200代表啟動成功，在瀏覽器輸入http://127.0.0.1:9200/，就會看到Elasticsearch的資訊

![elastic1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic1.png>)



![elastic2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic2.png>)



### 3.下載Logstash

https://www.elastic.co/cn/downloads/logstash

下載完後並解壓縮



### 4.新增配置檔案

Logstash需要一個檔案，指定使用檔案中的事件做事情，新增logstash.conf檔案，並且放在bin資料夾裡

以下範例：logstash.conf

```json
input { 
    file { 
        type => "server" 
        codec =>json #讀取文件的位置，一定準確，否則elasticsearch讀取不到 
        path =>"C:\Users\user\Desktop\ELK\log\laravel.json" 
    } 
} 
filter { 
    grok { 
        match => { 
            "message" => "%{TIMESTAMP_ISO8601:timestamp}\s+%{LOGLEVEL:severity}\s+\[%{DATA:service},%{DATA:trace},%{DATA:span},%{DATA:exportable}\]\s+%{DATA:pid}\s+---\s+\[%{DATA:thread}\]\s+%{DATA:class}\s+:\s+%{GREEDYDATA:rest}" 
        } 
    } 
} 
output { 
    elasticsearch { 
        hosts => ["localhost:9200"] #寫入到elasticsearch中的地址信息 
        index => "trace_test_log" #文檔索引 
    } 
}
```

##### 檔案有3個項目:

1. input:主要是從file取得檔案路徑，要在對應路徑底下新增空白檔案。
2. filter:是將message組變數路徑，發出訊息。
3. output:是網頁的host寫入Log檔案,，index則是快速找到Log檔案。

1、3要驗證正確性



新增空白檔案後，在bin的目錄底下執行指令，成功之後，在瀏覽器輸入http://127.0.0.1:9200/，就會看到Logsearch的資訊

```javascript
logstash -f logstash.conf
```



![elastic3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic3.png>)

![elastic4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic4.png>)



### 5.下載Kibana

https://www.elastic.co/cn/downloads/kibana

下載完後並解壓縮



### 6.啟動Kibana

在bin的資料夾裡，執行kibana.bat，看到127.0.0.1:5601代表啟動成功，在瀏覽器輸入http://127.0.0.1:5601/，就會看到kibana web界面，代表已經安裝成功。

![elastic5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic5.png>)

![elastic6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic6.png>)



## 在ELK建立Log

以下介紹，使用laravel安裝套件，並且建立Log

### 1.新增索引

點選左上角的menu，選擇Management的Stack Management，之後在點選index Patterns，輸入索引值後點選Create

以下範例已建立兩個索引值，例如:elastic*，可以Log過濾elastic以外的資料。

![elastic7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic7.png>)

![elastic8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic8.png>)



### 2.建立Log

以下範例使用laravel 建立Elastic Log

#### (1)安裝Elasticsearch套件

```php
composer require elasticsearch/elasticsearch
```



#### (2)建立設定檔，放在config資料夾裡

elastic.php

```php
<?php

$hosts = [
    '127.0.0.1:9200',   // IP + Port
    '127.0.0.1',    // Just IP
    'mydomain.server.com:9201', // Domain + Port
    'mydomain2.server.com',     // Just Domain
    'https://localhost',        // SSL to localhost
    'https://127.0.0.1:9200'  // SSL to IP + Port
];

return ['hosts' => $hosts];

?>
```



#### (3)建立Services，新增Services資料夾，放在app底下

ElasticService.php

```php
<?php

namespace App\Services;

use Illuminate\Database\Eloquent\Collection;
use Elasticsearch\ClientBuilder;
use Config;

class ElasticService
{
    public function connElastic()
    {
        $elastic = Config::get('elastic');

        $client = ClientBuilder::create()->setHosts($elastic['hosts'])->build();

        return $client;
    }

    public function createElastic($client, $data)
    {
        $response = $client->create($data);
    }

    public function updateElastic($client, $data)
    {
        $response = $client->update($data);
    }

    public function deleteElastic($client, $data)
    {
        $response = $client->delete($data);
    }

    public function addElastic($id, $name)
    {
        $params =[
            'index' => 'elastic' . date('YmdHms'),
            'type' => 'data',
            'id' => 'el1'
        ];
    
        $params['body'] = [
        	'id' => $id,
        	'name' => $name
        ];
    
        $client = $this->connElastic();
        $this->createElastic($client, $params);
    }
}

```



#### (4)在Controllers建立檔案，執行建立Log

在web設定route執行Controller的function

```php
Route::get('/elastic','TestController@testElastic');
```

ElasticServiceController.php

```php
<?php

namespace App\Http\Controllers;

use App\Services\ElasticService;
use Illuminate\Http\Request;

class TestController extends Controller
{
    protected $elasticService;

    public function __construct(ElasticService $elasticService)
    {
        $this->elasticService = $elasticService;
    }


    public function testElastic()
    {
        $client = $this->elasticService->addElastic(2, 'Ming');
		print_r('已建立成功!')
    }
}

```

### 3.確認Log

執行完之後，回到Kibana 介面，點選Discover

![elastic9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic9.png>)

![elastic10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic10.png>)



參考資料:

https://kknews.cc/zh-tw/code/qljaxbb.html、

https://www.google.com.tw/url?sa=i&url=https%3A%2F%2Fwww.awoo.com.tw%2Fblog%2Fgoogle-analytics%2F&psig=AOvVaw12rVYYZ7uRBFN3uDo7SZrK&ust=1596390215197000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCLia7v3I-uoCFQAAAAAdAAAAABAD、

https://github.com/cviebrock/laravel-elasticsearch、

https://www.elastic.co/guide/cn/elasticsearch/php/current/_configuration.html