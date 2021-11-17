## Elasticsearch

### 大綱

- 介紹
- Index
- Mapping
  - _index
  - _type
  - _id
  - _version
  - _scource
  - _size
  - _doc_count
  - _field_names
  - _ingnored
  - _routing
  - _meta
  - _tier
- Search
- CURD
- Query DSL
  - 
- Dev Tools

### 介紹

elasticsearch可以用索引值來做資料搜尋，有利於程式的logs或數據做篩選，Elasticsearch 建立每一筆資料時，都以JSON document方式呈現

- 定義 index 中每個 term 的名稱

- 定義每個 term 的資料型態，例如：string, Interger, boolean

- term & inverted index 的相關配置 (要使用哪個 Analyzer，或是不被索引)

- 包含多個 Key/Value 組合，就像是資料庫中的一筆資料
- 但跟資料庫不一樣的是，JSON 格式靈活不受限，不須預先定義格式
- 每個 Key/Value 的類型(string, number, boolean … etc) 可以自己指定或是由 Elasticsearch 幫忙推算

### Index

在Kibnna裡，會看到每筆資料都會有index，每筆index都有記錄，可以存放哪些欄位以及搜尋設定，index有3個部分組成

- data：由document 組成
- mapping：設定欄位與搜尋範圍
- setting：設定篩選

```php
{
  "_index" : "elsatic",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}

```

1. _index





### Mapping

elasticsearch 的Mapping，是以metadata fields組成的

[metadata fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-fields.html),

- _index

  - 索引值，可以當我每筆資料的ID

  - 如果索引值需要做多筆查詢時，有時需要添加索引相關的子查詢。

    ```json
    //更新id為1，index_1的text資料
    PUT index_1/_doc/1
    {
      "text": "Document in index 1"
    }
    
    //更新id為2，index_2的text資料後，做重新整理
    PUT index_2/_doc/2?refresh=true
    {
      "text": "Document in index 2"
    }
    
    //查詢index_1和index_2的資料，並且加上篩選條件
    GET index_1,index_2/_search
    {
      "query": {
        "terms": {
          "_index": ["index_1", "index_2"] //查詢索引值有index_1或index_2的字段
        }
      },
      "aggs": {
        "indices": {
          "terms": {
            "field": "_index", //取得索引值10筆資料
            "size": 10
          }
        }
      },
      "sort": [
        {
          "_index": { 
            "order": "asc" //用索引值當排序，由小至大
          }
        }
      ],
      "script_fields": {
        "index_name": {
          "script": {
            "lang": "painless",
            "source": "doc['_index']" //擷取索引值的ld中doc，這個文字資料
          }
        }
      }
    }
    ```

- _type

  - 存放資料型態

  - **這裡要注意6.0以上就棄用了**

    ```json
    //更新my-index-000001的doc，id為1的資料，做完後重新整理
    PUT my-index-000001/_doc/1?refresh=true
    {
      "text": "Document with type 'doc'"
    }
    
    //查詢my-index-000001的資料，並且加上篩選條件
    GET my-index-000001/_search
    {
      "query": {
        "term": {
          "_type": "_doc"  //查詢type為_doc的資料
        }
      },
      "aggs": {
        "types": {
          "terms": {
            "field": "_type", //取得type10筆資料
            "size": 10
          }
        }
      },
      "sort": [
        {
          "_type": { 
            "order": "desc" //用type當排序，由小至大
          }
        }
      ],
      "script_fields": {
        "type": {
          "script": {
            "lang": "painless",
            "source": "doc['_type']"//擷取索引值的type中doc，這個文字資料
          }
        }
      }
    
    ```

- _id

  - 唯一值

    ```json
    // 更新my-index-000001的doc，id為1的資料
    PUT my-index-000001/_doc/1
    {
      "text": "Document with ID 1"
    }
    
    // 更新my-index-000001的doc，id為2的資料，做完後重新整理
    PUT my-index-000001/_doc/2?refresh=true
    {
      "text": "Document with ID 2"
    }
    // 查詢my-index-000001的資料，id為1和2篩選條件
    GET my-index-000001/_search
    {
      "query": {
        "terms": {
          "_id": [ "1", "2" ] 
        }
      }
    }
    ```

  

- _scource

  - 存在放資料

    ```json
    // 更新my-index-000001的禁止資料存取
    PUT my-index-000001
    {
      "mappings": {
        "_source": {
          "enabled": false
        }
      }
    }
    
    PUT logs
    {
      "mappings": {
        "_source": {
          "includes": [
            "*.count",
            "meta.*"
          ],
          "excludes": [
            "meta.description",
            "meta.other.*"
          ]
        }
      }
    }
    
    PUT logs/_doc/1
    {
      "requests": {
        "count": 10,
        "foo": "bar" 
      },
      "meta": {
        "name": "Some metric",
        "description": "Some metric description", 
        "other": {
          "foo": "one", 
          "baz": "two" 
        }
      }
    }
    
    GET logs/_search
    {
      "query": {
        "match": {
          "meta.other.foo": "one" 
        }
      }
    }
    ```
    
    

- _size

  - 檔案大小

- _doc_count

  - doc有多少筆資料

    ```json
    PUT my_index
    {
      "mappings" : {
        "properties" : {
          "my_histogram" : {
            "type" : "histogram"
          },
          "my_text" : {
            "type" : "keyword"
          }
        }
      }
    }
    ```

    ```
    PUT my_index/_doc/1
    {
      "my_text" : "histogram_1",
      "my_histogram" : {
          "values" : [0.1, 0.2, 0.3, 0.4, 0.5],
          "counts" : [3, 7, 23, 12, 6]
       },
      "_doc_count": 45 
    }
    
    PUT my_index/_doc/2
    {
      "my_text" : "histogram_2",
      "my_histogram" : {
          "values" : [0.1, 0.25, 0.35, 0.4, 0.45, 0.5],
          "counts" : [8, 17, 8, 7, 6, 2]
       },
      "_doc_count": 62 
    }
    ```

    

- _field_names

  - 欄位名稱

- _ingnored

- _routing

- _meta

- _tier

  

  

  

#### **document metadata** 

描述 document 本身屬性用的資料，通常會包含以下內容：

- `_index`：document 所屬的 index 名稱
- `_type`：document 類型 (例如：**_doc**)
- `_id`：document ID
- `_source`：document 的原始 JSON 資料樣貌
- `_version`：版本訊息 (有這欄位就表示 ES 具有版本控管的能力)
- `_score`：查詢時的算分結果 (每次的搜尋都會根據 document 對於搜尋內容的相關度進行算分)

#### Index

- `index` 在 ES 中是個邏輯空間的概念，用來儲存 document 的容器，而這些 document 內容都是相似的 (跟其他領域的 index 用法不太一樣)
- `shard` 在 ES 中則是個物理空間的的概念，**index 中的資料會分散放在不同的 shard 中**
- index 由以下幾個部份組成：
  - `data`：由 document + metadata 所組成
  - `mapping`：用來定義每個欄位名稱 & 類型
  - `setting`：定義資料是如何存放(例如：replication 數量, 使用的 shard 數量)

範例

```
"properties" => [
    "title" => [
        "type" => "string",
        "boost" => 1.5,
        "analyzer" => "standard",
        "fields" => [
            "english" => [
                "type" => "string",
                "analyzer" => "standard",
                "search_analyzer" => "english",
                "boost" => 1.0
            ],
            "synonym" => [
                "type" => "string",
                "analyzer" => "standard",
                "search_analyzer" => "synonym",
                "boost" => 0.5
            ]
        ]
    ]
]

"settings"=> [
    "analysis" => [
        "analyzer" => [
            "synonym" => [
                "type" => "custom",
                "tokenizer" => "standard",
                "filter" => ["lowercase", "trim", "synonym"],
                "char_filter" => ["html_strip"]
            ]
        ],
        "filter" => [
            "synonym" => [
                "type" => "synonym",
                "synonyms_path" => "analysis/synonyms.txt"
            ]
        ]
    ]
]
```





### CURD

Elastic 也可以用url+get參數來查看資料，對應SQL的CURD，改用寫法

![elasticDevTool2](C:\xampp\htdocs\markdown_note\assets\images\elasticDevTool2.png)



- `Create`(**PUT**)：
  - 建立新的 document，如果 ID 已經存在會發生錯誤
  - 語法為 `PUT _index/_create/[ID]` or `PUT _index/_doc/[ID]?op_type=create`，例如：**PUT /users/_create/1** (也可以不帶 ID，就會自動生成)
  - **較不建議指定 ID 的作法，可能會撞到效能不彰的問題**
- `Create`(**POST**)
  - 系統會自動產生 document ID (**這是比較建議的方式**)
  - 語法為 `POST _index/_doc`
- `Index`(**PUT**)：
  - 如果 ID 不存在，則建立新的 document；若 ID 已經存在，則刪除現存的 document 再建立新的，**version** 的部份會增加
  - 語法為 [`PUT _index/_doc/[ID\]`](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)，例如：**PUT /users/_doc/1**
- `Update`(**PUT**)：
  - PUT 其實也可以作為更新 document 用，但更新的範圍是整個 document
  - 實際上，Elasticsearch document 是無法修改的；而更新這個操作其實是新增一個新的 document，將原有的 **_version** 加 1 後，舊的 document 被標示為 **deletion**
- `Partially Update`(**POST**)：
  - document 必須已經存在，更新時只會對 document 中相對應的欄位作增量更新 or 對應欄位的修改
  - json payload 需要包含在 `doc` 欄位中 (可參考[官網文件](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html))
  - 語法為 [`POST _index/_update/[ID\]`](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html)，例如：**POST /users/_update/1**
  - POST 也可以拿來作為新增 document 用

**查詢資料**

```
GET /_search
{
  "query": { 
    "bool": {  //超過兩個條件的時候要用bool包
      "must": [  //must:AND  should:OR
        { "match": { "title":   "Search"        }}, 
        { "match": { "content": "Elasticsearch" }}  
      ],
      "filter": [   //撈完顯示部分
        { "term":  { "status": "published" }}, 
        { "range": { "publish_date": { "gte": "2015-01-01" }}} //gte:greater-than-equal
      ]
    }
  }
}
```

要補充POST、DELETE 、PUT說明

```
PUT /customer?pretty
```



```
DELETE /customer?pretty
```



```
POST /customer/doc/1/_update?pretty
{
  "doc":{"yeat": 2018}
}
```





### Query DSL

- Query and filter context

  - Query context(查詢文字)

    用search API在query參數，來查詢mapping裡有符合_score欄位的資料

  - filter context

    ```json
    GET /_search
    {
      "query": { 
        "bool": { 
          "must": [
            { "match": { "title":   "Search"        }},
            { "match": { "content": "Elasticsearch" }}
          ],
          "filter": [ 
            { "term":  { "status": "published" }},
            { "range": { "publish_date": { "gte": "2015-01-01" }}}
          ]
        }
      }
    }
    ```

    

- 



### Dev Tools

Elasticsearch中的Kibana有個DevTool可以做設定，今天要介紹如何在Kibana上操作DevTool

![elasticDevTool1](C:\xampp\htdocs\markdown_note\assets\images\elasticDevTool1.png)

在左側寫好資料後，點選三角形圖示，會對應顯示Mapping的資料，可以用GET、POST、PUT、DELETE做CURD



參考資料:

https://kknews.cc/zh-tw/code/qljaxbb.html、

https://www.google.com.tw/url?sa=i&url=https%3A%2F%2Fwww.awoo.com.tw%2Fblog%2Fgoogle-analytics%2F&psig=AOvVaw12rVYYZ7uRBFN3uDo7SZrK&ust=1596390215197000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCLia7v3I-uoCFQAAAAAdAAAAABAD、

https://github.com/cviebrock/laravel-elasticsearch、

https://www.elastic.co/guide/cn/elasticsearch/php/current/_configuration.html

https://dotblogs.com.tw/supershowwei/2016/05/17/142101