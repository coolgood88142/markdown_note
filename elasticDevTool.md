## Elasticsearch DevTool

Elasticsearch中的Kibana有個DevTool可以做設定，今天要介紹如何在Kibana上操作DevTool

![elasticDevTool1](C:\xampp\htdocs\markdown_note\assets\images\elasticDevTool1.png)

在左側寫好資料後，點選三角形圖示，會對應顯示Mapping的資料，可以用GET、POST、PUT、DELETE做CURD

### Elasticsearch CURD

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



### Elasticsearch Mapping

Elasticsearch 建立每一筆資料時，都以JSON document方式呈現，而mapping先當於是Elasticsearch的Schema

- 定義 index 中每個 term 的名稱

- 定義每個 term 的資料型態，例如：string, Interger, boolean

- term & inverted index 的相關配置 (要使用哪個 Analyzer，或是不被索引)

  

- 包含多個 Key/Value 組合，就像是資料庫中的一筆資料
- 但跟資料庫不一樣的是，JSON 格式靈活不受限，不須預先定義格式
- 每個 Key/Value 的類型(string, number, boolean … etc) 可以自己指定或是由 Elasticsearch 幫忙推算

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



參考資料:

https://kknews.cc/zh-tw/code/qljaxbb.html、

https://www.google.com.tw/url?sa=i&url=https%3A%2F%2Fwww.awoo.com.tw%2Fblog%2Fgoogle-analytics%2F&psig=AOvVaw12rVYYZ7uRBFN3uDo7SZrK&ust=1596390215197000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCLia7v3I-uoCFQAAAAAdAAAAABAD、

https://github.com/cviebrock/laravel-elasticsearch、

https://www.elastic.co/guide/cn/elasticsearch/php/current/_configuration.html