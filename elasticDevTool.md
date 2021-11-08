## Elasticsearch DevTool

Elasticsearch中的Kibana有個DevTool可以做設定，今天要介紹如何在Kibana上操作DevTool

![elasticDevTool1](C:\xampp\htdocs\markdown_note\assets\images\elasticDevTool1.png)

在左側寫好資料後，點選三角形圖示，會對應顯示Mapping的資料

### Elasticsearch CRUD

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