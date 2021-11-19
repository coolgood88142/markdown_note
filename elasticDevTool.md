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

- data type 

  - common types

    - binary

      設定某個字串不可搜尋，但是字串要符合`Base64` 編碼

      ```json
      //更新my-index-00000的doc，id為1的資料，不可以搜尋U29tZSBiaW5hcnkgYmxvYg
      PUT my-index-000001/_doc/1
      {
        "name": "Some binary blob",
        "blob": "U29tZSBiaW5hcnkgYmxvYg" 
      }
      ```

    - boolean

      

      ```json
      //更新my-index-00000的doc，id為1的資料，設定is_published為true
      POST my-index-000001/_doc/1?refresh
      {
        "is_published": "true" 
      }
      
      GET my-index-000001/_search
      {
        "query": {
          "term": {
            "is_published": true 
          }
        }
      }
      ```
  
    - keywords
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "tags": {
              "type":  "keyword"
            }
          }
        }
      }
      ```
  
      
  
    - numbers
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "number_of_bytes": {
              "type": "integer"
            },
            "time_in_seconds": {
              "type": "float"
            },
            "price": {
              "type": "scaled_float",
              "scaling_factor": 100
            }
          }
        }
      }
      ```
  
      
  
    - dates
  
      設定日期要直接
  
    - alias
  
      ```
      PUT trips
      {
        "mappings": {
          "properties": {
            "distance": {
              "type": "long"
            },
            "route_length_miles": {
              "type": "alias",
              "path": "distance" 
            },
            "transit_mode": {
              "type": "keyword"
            }
          }
        }
      }
      ```
  
      
  
  - Obeject  and relational types
  
    - Object
  
      ```
      PUT my-index-000001/_doc/1
      { 
        "region": "US",
        "manager": { 
          "age":     30,
          "name": { 
            "first": "John",
            "last":  "Smith"
          }
        }
      }
      ```
  
      
  
    - flattened
  
      ```
      PUT bug_reports
      {
        "mappings": {
          "properties": {
            "title": {
              "type": "text"
            },
            "labels": {
              "type": "flattened"
            }
          }
        }
      }
      
      POST bug_reports/_doc/1
      {
        "title": "Results are not sorted correctly.",
        "labels": {
          "priority": "urgent",
          "release": ["v1.2.5", "v1.3.0"],
          "timestamp": {
            "created": 1541458026,
            "closed": 1541457010
          }
        }
      }
      ```
  
      
  
    - nested
  
      ```
      PUT my-index-000001/_doc/1
      {
        "group" : "fans",
        "user" : [ 
          {
            "first" : "John",
            "last" :  "Smith"
          },
          {
            "first" : "Alice",
            "last" :  "White"
          }
        ]
      }
      ```
  
      
  
    - join
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "my_id": {
              "type": "keyword"
            },
            "my_join_field": { 
              "type": "join",
              "relations": {
                "question": "answer" 
              }
            }
          }
        }
      }
      ```
  
      
  
  - Structured data types
  
    - range
  
      ```
      PUT range_index
      {
        "settings": {
          "number_of_shards": 2
        },
        "mappings": {
          "properties": {
            "expected_attendees": {
              "type": "integer_range"
            },
            "time_frame": {
              "type": "date_range", 
              "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            }
          }
        }
      }
      
      PUT range_index/_doc/1?refresh
      {
        "expected_attendees" : { 
          "gte" : 10,
          "lt" : 20
        },
        "time_frame" : {
          "gte" : "2015-10-31 12:00:00", 
          "lte" : "2015-11-01"
        }
      }
      ```
  
      
  
    - ip
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "ip_addr": {
              "type": "ip"
            }
          }
        }
      }
      
      PUT my-index-000001/_doc/1
      {
        "ip_addr": "192.168.1.1"
      }
      
      GET my-index-000001/_search
      {
        "query": {
          "term": {
            "ip_addr": "192.168.0.0/16"
          }
        }
      }
      
      ```
  
      
  
    - version
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "my_version": {
              "type": "version"
            }
          }
        }
      }
      ```
  
      
  
    - murmur3
  
      ```
      sudo bin/elasticsearch-plugin install mapper-murmur3
      ```
  
      
  
  - Aggregate data types
  
    - aggregate_metric_double
  
      ```
      PUT my-index
      {
        "mappings": {
          "properties": {
            "my-agg-metric-field": {
              "type": "aggregate_metric_double",
              "metrics": [ "min", "max", "sum", "value_count" ],
              "default_metric": "max"
            }
          }
        }
      }
      ```
  
      
  
    - historgram
  
      - [min](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-min-aggregation.html#search-aggregations-metrics-min-aggregation-histogram-fields) aggregation
      - [max](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-max-aggregation.html#search-aggregations-metrics-max-aggregation-histogram-fields) aggregation
      - [sum](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-sum-aggregation.html#search-aggregations-metrics-sum-aggregation-histogram-fields) aggregation
      - [value_count](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-valuecount-aggregation.html#search-aggregations-metrics-valuecount-aggregation-histogram-fields) aggregation
      - [avg](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-avg-aggregation.html#search-aggregations-metrics-avg-aggregation-histogram-fields) aggregation
      - [percentiles](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-percentile-aggregation.html) aggregation
      - [percentile ranks](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-percentile-rank-aggregation.html) aggregation
      - [boxplot](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-boxplot-aggregation.html) aggregation
      - [histogram](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-histogram-aggregation.html#search-aggregations-bucket-histogram-aggregation-histogram-fields) aggregation
      - [range](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-range-aggregation.html#search-aggregations-bucket-range-aggregation-histogram-fields) aggregation
      - [exists](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-exists-query.html) query
  
      ```
      PUT my-index-000001/_doc/1
      {
        "my_text" : "histogram_1",
        "my_histogram" : {
            "values" : [0.1, 0.2, 0.3, 0.4, 0.5], 
            "counts" : [3, 7, 23, 12, 6] 
         }
      }
      
      PUT my-index-000001/_doc/2
      {
        "my_text" : "histogram_2",
        "my_histogram" : {
            "values" : [0.1, 0.25, 0.35, 0.4, 0.45, 0.5], 
            "counts" : [8, 17, 8, 7, 6, 2] 
         }
      }
      ```
  
      
  
    - Text search types
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "full_name": {
              "type":  "text"
            }
          }
        }
      }
      ```
  
      
  
    - annotated-text
  
      ```
      sudo bin/elasticsearch-plugin install mapper-annotated-text
      ```
  
      
  
    - completion
  
      ```
      UT place
      {
        "mappings": {
          "properties": {
            "suggest": {
              "type": "completion",
              "contexts": [
                {                                 
                  "name": "place_type",
                  "type": "category"
                },
                {                                 
                  "name": "location",
                  "type": "geo",
                  "precision": 4
                }
              ]
            }
          }
        }
      }
      PUT place_path_category
      {
        "mappings": {
          "properties": {
            "suggest": {
              "type": "completion",
              "contexts": [
                {                           
                  "name": "place_type",
                  "type": "category",
                  "path": "cat"
                },
                {                           
                  "name": "location",
                  "type": "geo",
                  "precision": 4,
                  "path": "loc"
                }
              ]
            },
            "loc": {
              "type": "geo_point"
            }
          }
        }
      }
      ```
  
      
  
    - search_as_you_type
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "my_field": {
              "type": "search_as_you_type"
            }
          }
        }
      }
      ```
  
      
  
    - token_count
  
      ```
      UT my-index-000001
      {
        "mappings": {
          "properties": {
            "name": { 
              "type": "text",
              "fields": {
                "length": { 
                  "type":     "token_count",
                  "analyzer": "standard"
                }
              }
            }
          }
        }
      }
      
      PUT my-index-000001/_doc/1
      { "name": "John Smith" }
      
      PUT my-index-000001/_doc/2
      { "name": "Rachel Alice Williams" }
      
      GET my-index-000001/_search
      {
        "query": {
          "term": {
            "name.length": 3 
          }
        }
      }
      
      ```
  
      
  
  - Document ranking types
  
    - dense_vector
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "my_vector": {
              "type": "dense_vector",
              "dims": 3  
            },
            "my_text" : {
              "type" : "keyword"
            }
          }
        }
      }
      
      PUT my-index-000001/_doc/1
      {
        "my_text" : "text1",
        "my_vector" : [0.5, 10, 6]
      }
      
      PUT my-index-000001/_doc/2
      {
        "my_text" : "text2",
        "my_vector" : [-0.5, 10, 10]
      }
      ```
  
      
  
    - sparse_vector
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "my_vector": {
              "type": "sparse_vector"
            },
            "my_text" : {
              "type" : "keyword"
            }
          }
        }
      }
      
      PUT my-index-000001/_doc/1
      {
        "my_text" : "text1",
        "my_vector" : {"1": 0.5, "5": -0.5,  "100": 1}
      }
      
      PUT my-index-000001/_doc/2
      {
        "my_text" : "text2",
        "my_vector" : {"103": 0.5, "4": -0.5,  "5": 1, "11" : 1.2}
      }
      
      ```
  
      
  
    - rank_features
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "pagerank": {
              "type": "rank_feature" 
            },
            "url_length": {
              "type": "rank_feature",
              "positive_score_impact": false 
            }
          }
        }
      }
      
      PUT my-index-000001/_doc/1
      {
        "pagerank": 8,
        "url_length": 22
      }
      
      GET my-index-000001/_search
      {
        "query": {
          "rank_feature": {
            "field": "pagerank"
          }
        }
      }
      ```
  
      
  
  - Spatial data types
  
    - geo_point
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "location": {
              "type": "geo_point"
            }
          }
        }
      }
      
      PUT my-index-000001/_doc/1
      {
        "text": "Geopoint as an object",
        "location": { 
          "lat": 41.12,
          "lon": -71.34
        }
      }
      
      PUT my-index-000001/_doc/2
      {
        "text": "Geopoint as a string",
        "location": "41.12,-71.34" 
      }
      
      PUT my-index-000001/_doc/3
      {
        "text": "Geopoint as a geohash",
        "location": "drm3btev3e86" 
      }
      
      PUT my-index-000001/_doc/4
      {
        "text": "Geopoint as an array",
        "location": [ -71.34, 41.12 ] 
      }
      
      PUT my-index-000001/_doc/5
      {
        "text": "Geopoint as a WKT POINT primitive",
        "location" : "POINT (-71.34 41.12)" 
      }
      
      GET my-index-000001/_search
      {
        "query": {
          "geo_bounding_box": { 
            "location": {
              "top_left": {
                "lat": 42,
                "lon": -72
              },
              "bottom_right": {
                "lat": 40,
                "lon": -74
              }
            }
          }
        }
      }
      ```
  
      
  
    - geo_shape
  
      - `distance_error_pct`
      - `points_only`
      - `precision`
      - `strategy`
      - `tree_levels`
      - `tree`
  
      
  
    - point
  
      ```
      PUT my-index-000001
      {
        "mappings": {
          "properties": {
            "location": {
              "type": "point"
            }
          }
        }
      }
      
      PUT my-index-000001/_doc/1
      {
        "text": "Point as an object",
        "location": { 
          "x": 41.12,
          "y": -71.34
        }
      }
      
      PUT my-index-000001/_doc/2
      {
        "text": "Point as a string",
        "location": "41.12,-71.34" 
      }
      
      
      PUT my-index-000001/_doc/4
      {
        "text": "Point as an array",
        "location": [41.12, -71.34] 
      }
      
      PUT my-index-000001/_doc/5
      {
        "text": "Point as a WKT POINT primitive",
        "location" : "POINT (41.12 -71.34)" 
      }
      ```
  
      
  
    - shape
  
      ```
      PUT /example
      {
        "mappings": {
          "properties": {
            "geometry": {
              "type": "shape"
            }
          }
        }
      }
      ```
  
      
  
  - Other type
    - percolator
  
      由官方的Query DSL介紹

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

    在查詢條件裡，指定某幾個欄位做篩選條件

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

    查詢條件：

    1. title欄位有包含Search文字
    2. content欄位有包含Elasticsearch文字
    3. status資料為published
    4. 查publish_date資料2015-01-01以後的資料

- Compound queries(複合查詢)

  使用多種條件，去搜尋欄位中的資料

  - bool(布林值)

    - must：有符合的字段
    - filter：過濾欄位查詢
    - should：有包含的字段
    - must_not：不包含的字段

    ```
    POST _search
    {
      "query": {
        "bool" : {
          "must" : {
            "term" : { "user.id" : "kimchy" }
          },
          "filter": {
            "term" : { "tags" : "production" }
          },
          "must_not" : {
            "range" : {
              "age" : { "gte" : 10, "lte" : 20 }
            }
          },
          "should" : [
            { "term" : { "tags" : "env1" } },
            { "term" : { "tags" : "deployed" } }
          ],
          "minimum_should_match" : 1,
          "boost" : 1.0
        }
      }
    }
    ```

  - boosting(提升方法)

    - positive：查詢所有的類別資料
    - negative：排除指定類別資料
    - negative_boost：使用negative方法，指定分數0到，用浮點數表示

    ```
    GET /_search
    {
      "query": {
        "boosting": {
          "positive": {
            "term": {
              "text": "apple"
            }
          },
          "negative": {
            "term": {
              "text": "pie tart fruit crumble tree"
            }
          },
          "negative_boost": 0.5
        }
      }
    }
    ```

  - constant_score(過濾子查詢)

    - filter：過濾欄位查詢
    - boost：查詢的分數

    ```
    GET /_search
    {
      "query": {
        "constant_score": {
          "filter": {
            "term": { "user.id": "kimchy" }
          },
          "boost": 1.2
        }
      }
    }
    ```

  - dis_max

    - queries：包含一個或多個查詢條件

    ```
    GET /_search
    {
      "query": {
        "dis_max": {
          "queries": [
            { "term": { "title": "Quick pets" } },
            { "term": { "body": "Quick pets" } }
          ],
          "tie_breaker": 0.7
        }
      }
    }
    ```

  - function_score(功能查詢)

    ```
    GET /_search
    {
      "query": {
        "function_score": {
          "query": { "match_all": {} },
          "boost": "5", 
          "functions": [
            {
              "filter": { "match": { "test": "bar" } },
              "random_score": {}, 
              "weight": 23
            },
            {
              "filter": { "match": { "test": "cat" } },
              "weight": 42
            }
          ],
          "max_boost": 42,
          "score_mode": "max",
          "boost_mode": "multiply",
          "min_score": 42
        }
      }
    }
    ```

- Full text queries

  - Intervals(間隔查詢)

    - match
    - prefix
    - wildcard
    - fuzzy
    - all_of
    - any_of

    ```
    POST _search
    {
      "query": {
        "intervals" : {
          "my_text" : {
            "all_of" : {
              "ordered" : true,
              "intervals" : [
                {
                  "match" : {
                    "query" : "my favorite food",
                    "max_gaps" : 0,
                    "ordered" : true
                  }
                },
                {
                  "any_of" : {
                    "intervals" : [
                      { "match" : { "query" : "hot water" } },
                      { "match" : { "query" : "cold porridge" } }
                    ]
                  }
                }
              ]
            }
          }
        }
      }
    }
    ```

    

  - Match

  - Match boolean prefix

  - Match phrase

  - Match phrase prefix

  - Combined fields

  - Multi-match

  - Common Terms Query

  - Query string

  - Simple query string

- Geo queries

  - Geo-bounding box
  - Geo-distance
  - Geo-polygon
  - Geoshape

- Shape queries

- Joining queries

  - Nested
  - Has child
  - Has parent
  - Parent ID

- Match all

- Span queries

  - Span containing
  - Span field masking
  - Span first
  - Span multi-term
  - Span near
  - Span not
  - Span or
  - Span term
  - Span within

- Specialized queries

  - Distance feature
  - More like this
  - Percolate
  - Rank feature
  - Script
  - Script score
  - Wrapper
  - Pinned Query

- Term-level queries

  - Exists
  - Fuzzy
    IDs
  - Prefix
  - Range
  - Regexp
  - Term
  - Terms
  - Terms set
  - Type Query
  - Wildcard

- minimum_should_match parameter

- rewrite parameter

- Regular expression syntax

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