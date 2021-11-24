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

      ```json
      //更新my-index-000001做關鍵字搜尋
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

      - long
      - integer
      - short
      - byte
      - double
      - float
      - half_float
      - scaled_float
      - unsigned_long

      ```json
      //更新my-index-000001的number_of_bytes欄位為nteger、time_in_seconds欄位為float、price欄位為long，但是用double資料型態表示
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

      設定日期資料型態

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

    - text fields

      - analyzer
      - boost
      - eager_global_ordinals
      - fielddata
      - fielddata_frequency_filter
      - fields
      - index
      - index_options
      - index_prefixes
      - index_phrases
      - norms
      - position_increment_gap
      - store
      - search_analyzer
      - search_quote_analyzer
      - similarity
      - term_vector
      - meta

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


- 

### Mapping與MySQL的Data Type差別

|      | Mapping | MySQL                                 |
| ---- | ------- | ------------------------------------- |
| 文字 | binary  | CHAR                                  |
| 日期 | Dates   | DATE、TIME、YEAR、DATETIME、TIMESTAMP |
| 數值 | Numbers | INTEGER、DOUBLE、FLOAT                |
|      |         |                                       |

https://ithelp.ithome.com.tw/articles/10241212

https://www.itread01.com/content/1564456805.html

https://www.itread01.com/content/1549391948.html

https://blog.tienyulin.com/elasticsearch-kibana-command-dsl-docker-compose/

https://www.runoob.com/mysql/mysql-data-types.html

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html


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

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    - match(取得相同的文字)

      - query：必填，查詢文字
      - max_gaps：設定文字的長度，預設值為0
      - ordered：設定找到的結果是否排序，預設值為false
      - analyzer：設定分詞，使用時把query更換成analyzer
      - filter：設定篩選的欄位
      - use_field：自行設定欄位做搜尋

    - prefix(正規化查詢)

      - prefix
      - anlyzer：設定分詞
      - use_field：自行設定欄位做搜尋

    - wildcard(萬用字元查詢)

      有包含* ? 等等符號做模糊查詢

      - pattern：
      - anlyzer：設定分詞
      - use_field：自行設定欄位做搜尋

    - fuzzy(模糊查詢)

      - term：搜尋文字的相關資料
      - prefix_length：
      - transpositions：搜尋文字是否要更換位置的資料，例如：cat->act
      - fuzziness：搜尋文字的長度
      - analyzer：設定分詞
      - use_field：自行設定欄位做搜尋

    - all_of

      - intervals：符合規則的文字資料
      - max_gaps：設定文字的長度，預設值為0
      - ordered：設定排序
      - filter：設定篩選的欄位

    - any_of

      - intervals：符合規則的文字資料
      - filter：設定篩選的欄位

    - filter

      - after：設定篩選欄位的
      - before
      - contained_by
      - containing
      - not_contained_by：設定不包含篩選欄位的間隔資料
      - not_containing：設定不包含篩選的欄位資料
      - not_overlapping
      - overlapping：設定間隔
      - script：設定腳本

    ```json
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

    - query：必填，查詢文字
    - analyzer：設定分詞，使用時把query更換成analyzer
    - auto_generate_synonyms_phrase_query：設定是否要用同義詞查詢
    - fuzziness：搜尋文字的長度
    - max_expansions：設定查詢件數，預設值為50
    - prefix_length：設定模糊查詢的從第幾個開始找
    - fuzzy_transpositions：設定模糊查詢找到的資料，可以做更換位置
    - fuzzy_rewrite：設定重複模糊查詢
    - lenient：設定是否要忽略格式錯誤
    - operator：設定query的值，修改查詢條件
      - or：query的值是capital of Hungary，查詢條件變成capital或of或Hungary，查詢文字有符合3個條件任1個的資料
      - and：query的值是capital of Hungary，查詢條件變成capital和of和Hungary，查詢文字有符合3個條件的資料
    - minimum_should_match
    - zero_terms_query：
      - none：刪除analyzer所有的標記資料
      - all：回傳analyzer所有的標記資料

    ```json
    GET /_search
    {
      "query": {
        "match": {
          "message": {
            "query": "this is a test"
          }
        }
      }
    }
    ```

    

  - Match boolean prefix

    

    ```json
    GET /_search
    {
      "query": {
        "match_bool_prefix": {
          "message": {
            "query": "quick brown f"
          }
        }
      }
    }
    ```

    

  - Match phrase

    搜尋資料中有符合文字的全部資料

    ```json
    GET /_search
    {
      "query": {
        "match_phrase": {
          "message": {
            "query": "this is a test",
            "analyzer": "my_analyzer"
          }
        }
      }
    }
    ```

    

  - Match phrase prefix

    - query
    - analyzer
    - max_expansions
    - slop
    - zero_terms_query

    ```json
    GET /_search
    {
      "query": {
        "match_phrase_prefix": {
          "message": {
            "query": "quick brown f"
          }
        }
      }
    }
    ```

    

  - Combined fields

    - fields：設定查詢的欄位名稱
    - query：設定查詢文字的內容
    - auto_generate_synonyms_phrase_query：設定是否要用同義詞查詢
    - operator：設定query的值，修改查詢條件
      - or：query的值是capital of Hungary，查詢條件變成capital或of或Hungary，查詢文字有符合3個條件任1個的資料
      - and：query的值是capital of Hungary，查詢條件變成capital和of和Hungary，查詢文字有符合3個條件的資料
    - minimum_should_match
    - zero_terms_query
      - none：刪除analyzer所有的標記資料
      - all：回傳analyzer所有的標記資料

    ```json
    GET /_search
    {
      "query": {
        "combined_fields" : {
          "query":      "database systems",
          "fields":     [ "title", "abstract"],
          "operator":   "and"
        }
      }
    }
    ```

  - Multi-match

    type：

    - best_fields
    - most_fields
    - cross_fields
    - phrase
    - phrase_prefix
    - bool_prefix

    ```
    GET /_search
    {
      "query": {
        "multi_match" : {
          "query":    "this is a test", 
          "fields": [ "subject", "message" ] 
        }
      }
    }
    ```

    

  - Common Terms Query

    ```
    GET /_search
    {
      "query": {
        "common": {
          "body": {
            "query": "this is bonsai cool",
            "cutoff_frequency": 0.001
          }
        }
      }
    }
    ```

    

  - Query string

    - query
    - default_field
    - allow_leading_wildcard
    - analyze_wildcard
    - analyzer
    - auto_generate_synonyms_phrase_query
    - boost
    - default_operator
      - or
      - and
    - enable_position_increments
    - fields
    - fuzziness
    - fuzzy_max_expansions
    - fuzzy_prefix_length
    - fuzzy_transpositions
    - lenient
    - max_determinized_states
    - minimum_should_match
    - quote_analyzer
    - phrase_slop
    - quote_field_suffix
    - rewrite
    - time_zone

    ```
    GET /_search
    {
      "query": {
        "query_string": {
          "query": "(new york city) OR (big apple)",
          "default_field": "content"
        }
      }
    }
    ```

    

  - Simple query string

    - query
    - fields
    - default_operator
      - or
      - and
    - all_fields
    - analyze_wildcard
    - analyzer
    - auto_generate_synonyms_phrase_query
    - flags
    - fuzzy_max_expansions
    - fuzzy_prefix_length
    - fuzzy_transpositions
    - lenient
    - minimum_should_match
    - quote_field_suffix

    ```
    GET /_search
    {
      "query": {
        "simple_query_string" : {
            "query": "\"fried eggs\" +(eggplant | potato) -frittata",
            "fields": ["title^5", "body"],
            "default_operator": "and"
        }
      }
    }
    ```

    

- Geo queries(地理查詢)

  ```json
  //更新my_locations有地理座標資料
  PUT /my_locations
  {
    "mappings": {
      "properties": {
        "pin": {
          "properties": {
            "location": {
              "type": "geo_point"
            }
          }
        }
      }
    }
  }
  
  //更新my_locations的doc，id為1的地理資料，緯度40.12，經度-71.34
  PUT /my_locations/_doc/1
  {
    "pin": {
      "location": {
        "lat": 40.12,
        "lon": -71.34
      }
    }
  }
  
  //更新my_geoshapes有地理範圍資料
  PUT /my_geoshapes
  {
    "mappings": {
      "properties": {
        "pin": {
          "properties": {
            "location": {
              "type": "geo_shape"
            }
          }
        }
      }
    }
  }
  
  //更新my_geoshapes的doc，id為1的地理資料，設定某個地理範圍資料
  PUT /my_geoshapes/_doc/1
  {
    "pin": {
      "location": {
        "type" : "polygon",
        "coordinates" : [[[13.0 ,51.5], [15.0, 51.5], [15.0, 54.0], [13.0, 54.0], [13.0 ,51.5]]]
      }
    }
  }
  ```

  - Geo-bounding box(地理邊界查詢)

    ```json
    //查詢my_locations或my_geoshapes的pin location的資料，是否在緯度40.73 經度-74.1與緯度40.01 經度-71.12的之間
    GET my_locations,my_geoshapes/_search
    {
      "query": {
        "bool": {
          "must": {
            "match_all": {}
          },
          "filter": {
            "geo_bounding_box": {
              "pin.location": {
                "top_left": {
                  "lat": 40.73,
                  "lon": -74.1
                },
                "bottom_right": {
                  "lat": 40.01,
                  "lon": -71.12
                }
              }
            }
          }
        }
      }
    }
    ```

  - Geo-distance(地理距離查詢)

    ```json
    //查詢my_locations或my_geoshapes的pin location的資料，是否緯度40 經度-70座標方圓200公里範圍內
    GET my_locations,my_geoshapes/_search
    {
      "query": {
        "bool": {
          "must": {
            "match_all": {}
          },
          "filter": {
            "geo_distance": {
              "distance": "200km",
              "pin.location": {
                "lat": 40,
                "lon": -70
              }
            }
          }
        }
      }
    }
    ```

    

  - Geo-polygon

    7.12板後棄用

    ```json
    GET my_locations,my_geoshapes/_search
    {
      "query": {
        "bool": {
          "must": {
            "match_all": {}
          },
          "filter": {
            "geo_polygon": {
              "person.location": {
                "points": [
                  { "lat": 40, "lon": -70 },
                  { "lat": 30, "lon": -80 },
                  { "lat": 20, "lon": -90 }
                ]
              }
            }
          }
        }
      }
    }
    ```

    

  - Geoshape(地理形狀查詢)

    ```json
    //查詢example的geo_shape欄位
    GET /example/_search
    {
      "query": {
        "bool": {
          "must": {
            "match_all": {}
          },
          "filter": {
            "geo_shape": {
              "location": {
                "shape": {
                  "type": "envelope",
                  "coordinates": [ [ 13.0, 53.0 ], [ 14.0, 52.0 ] ]
                },
                "relation": "within"
              }
            }
          }
        }
      }
    }
    ```

    

- Shape queries(形狀查詢)

  Geoshape與Shape差別?

  ```json
  
  GET /example_points/_search
  {
    "query": {
      "bool": {
        "must": {
          "match_all": {}
        },
        "filter": {
          "geo_shape": {
            "location": {
              "shape": {
                "type": "envelope",
                "coordinates": [ [ 13.0, 53.0 ], [ 14.0, 52.0 ] ]
              },
              "relation": "intersects"
            }
          }
        }
      }
    }
  }
  ```

  

- Joining queries

  - Nested：巢狀型別查詢

    - path：要查詢對象的路徑
    - query：查詢對象的名稱
    - score_mode：查詢時用對象的_score來做查詢
      - avg：設定查詢的平均分數
      - max：設定查詢的最高分數
      - none：設定查詢的最低分數
      - sum：設定查詢的總和
    - ignore_unmapped：忽略path的錯誤搜尋

    ```json
    //更新drivers資料型態，設定driver為巢狀資料型態，有包含last_name、vehicle
    //last_name設定為text，vehicle設定為巢狀資料型態
    //vehicle有包含make、model，兩個都是設定為文字
    PUT /drivers
    {
      "mappings": {
        "properties": {
          "driver": {
            "type": "nested",
            "properties": {
              "last_name": {
                "type": "text"
              },
              "vehicle": {
                "type": "nested",
                "properties": {
                  "make": {
                    "type": "text"
                  },
                  "model": {
                    "type": "text"
                  }
                }
              }
            }
          }
        }
      }
    }
    
    //更新drivers的doc，id為1的資料，last_name為McQueen，vehicle設定兩組資料
    //第一組 make為Powell Motors mode為Canyonero
    //第二組 make為Miller-Meteor mode為Ecto-1
    PUT /drivers/_doc/1
    {
      "driver" : {
            "last_name" : "McQueen",
            "vehicle" : [
                {
                    "make" : "Powell Motors",
                    "model" : "Canyonero"
                },
                {
                    "make" : "Miller-Meteor",
                    "model" : "Ecto-1"
                }
            ]
        }
    }
    ```

    ```json
    //查詢drivers的巢狀資料型態的driver vehicle的資料，make有符合Powell Motor和model有符合Canyonero的資料
    GET /drivers/_search
    {
      "query": {
        "nested": {
          "path": "driver",
          "query": {
            "nested": {
              "path": "driver.vehicle",
              "query": {
                "bool": {
                  "must": [
                    { "match": { "driver.vehicle.make": "Powell Motors" } },
                    { "match": { "driver.vehicle.model": "Canyonero" } }
                  ]
                }
              }
            }
          }
        }
      }
    }
    ```

    

  - Has child(子類別查詢)

    - type：搜尋子關係的欄位名稱
    - query：設定子關係的搜尋文字
    - ignore_unmapped：忽略type的錯誤搜尋
    - max_children：設定子關係的最大件數
    - min_children：設定子關係的最小件數
    - score_mode：查詢時用子關係的_score來做查詢
      - avg：設定查詢的平均分數
      - max：設定查詢的最高分數
      - none：設定查詢的最低分數
      - sum：設定查詢的總和

    ```json
    //更新my-index-000001的my-join-field欄位，設定子類別
    PUT /my-index-000001
    {
      "mappings": 
        "properties": {
          "my-join-field": {
            "type": "join",
            "relations": {
              "parent": "child"
            }
          }
        }
      }
    }
    ```

    ```json
    //查詢所有的子關係，設定最多10筆，最少2筆
    GET /_search
    {
      "query": {
        "has_child": {
          "type": "child",
          "query": {
            "match_all": {}
          },
          "max_children": 10,
          "min_children": 2,
          "score_mode": "min"
        }
      }
    }
    ```

    

  - Has parent(父類別查詢)

    - parent_type：查詢父類別欄位名稱
    - query：搜尋父類別的文字資料，根據parent_type做搜尋
    - score：是否要查子類別
    - ignore_unmapped：忽略parent_type的錯誤搜尋

    ```json
    //更新my-index-000002的my-join-field欄位，設定子類別，以及tag欄位type為keyword
    PUT /my-index-000002
    {
      "mappings": {
        "properties": {
          "my-join-field": {
            "type": "join",
            "relations": {
              "parent": "child"
            }
          },
          "tag": {
            "type": "keyword"
          }
        }
      }
    }
    ```

    ```json
    //查詢my-index-000002的父類別，找tag欄位中value欄位有符合Elasticsearch的資料
    GET /my-index-000002/_search
    {
      "query": {
        "has_parent": {
          "parent_type": "parent",
          "query": {
            "term": {
              "tag": {
                "value": "Elasticsearch"
              }
            }
          }
        }
      }
    }
    ```

    

  - Parent ID(父類別ID查詢)

    ```json
    //更新my-index-000003的my-join-field欄位，建立父子類別，父類別(my-parent)對應子類別(my-child)
    PUT /my-index-000003
    {
      "mappings": {
        "properties": {
          "my-join-field": {
            "type": "join",
            "relations": {
              "my-parent": "my-child"
            }
          }
        }
      }
    }
    ```

    ```json
    //更新my-index-000003的doc，id為1，設定my-join-field欄位為父類別，text為This is a parent document.
    PUT /my-index-000003/_doc/1?refresh
    {
      "text": "This is a parent document.",
      "my-join-field": "my-parent"
    }
    ```

    ```json
    //更新my-index-000003的doc，id為2，設定my-join-field欄位為子類別，對應父類別id為1的資料，，text為This is a child document.
    PUT /my-index-000003/_doc/2?routing=1&refresh
    {
      "text": "This is a child document.",
      "my-join-field": {
        "name": "my-child",
        "parent": "1"
      }
    }
    ```

  

- Match all

  ```json
  //查詢所有資料，找出_score欄位為1.2分的資料
  GET /_search
  {
    "query": {
      "match_all": { "boost" : 1.2 }
    }
  }
  ```

  

- Span queries(跨越查詢)

  - Span containing

    ```json
    //查詢所有資料中，不限制欄位找出little的foo與big的bar跟baz的相同資料，但是有符合big的資料中，只抓前5筆
    GET /_search
    {
      "query": {
        "span_containing": {
          "little": {
            "span_term": { "field1": "foo" }
          },
          "big": {
            "span_near": {
              "clauses": [
                { "span_term": { "field1": "bar" } },
                { "span_term": { "field1": "baz" } }
              ],
              "slop": 5,
              "in_order": true
            }
          }
        }
      }
    }
    ```

    

  - Span field masking

    ```json
    //查詢所有資料中
    GET /_search
    {
      "query": {
        "span_near": {
          "clauses": [
            {
              "span_term": {
                "text": "quick brown"
              }
            },
            {
              "span_field_masking": {
                "query": {
                  "span_term": {
                    "text.stems": "fox"
                  }
                },
                "field": "text"
              }
            }
          ],
          "slop": 5,
          "in_order": false
        }
      }
    }
    ```

    

  - Span first

    ```json
    GET /_search
    {
      "query": {
        "span_first": {
          "match": {
            "span_term": { "user.id": "kimchy" }
          },
          "end": 3
        }
      }
    }
    ```

    

  - Span multi-term

    ```
    GET /_search
    {
      "query": {
        "span_multi": {
          "match": {
            "prefix": { "user.id": { "value": "ki" } }
          }
        }
      }
    }
    ```

    

  - Span near

    ```
    GET /_search
    {
      "query": {
        "span_near": {
          "clauses": [
            { "span_term": { "field": "value1" } },
            { "span_term": { "field": "value2" } },
            { "span_term": { "field": "value3" } }
          ],
          "slop": 12,
          "in_order": false
        }
      }
    }
    ```

    

  - Span not

    ```
    GET /_search
    {
      "query": {
        "span_not": {
          "include": {
            "span_term": { "field1": "hoya" }
          },
          "exclude": {
            "span_near": {
              "clauses": [
                { "span_term": { "field1": "la" } },
                { "span_term": { "field1": "hoya" } }
              ],
              "slop": 0,
              "in_order": true
            }
          }
        }
      }
    }
    ```

    

  - Span or

    ```
    GET /_search
    {
      "query": {
        "span_or" : {
          "clauses" : [
            { "span_term" : { "field" : "value1" } },
            { "span_term" : { "field" : "value2" } },
            { "span_term" : { "field" : "value3" } }
          ]
        }
      }
    }
    ```

    

  - Span term

    ```
    GET /_search
    {
      "query": {
        "span_term" : { "user.id" : "kimchy" }
      }
    }
    ```

    

  - Span within

    ```
    GET /_search
    {
      "query": {
        "span_within": {
          "little": {
            "span_term": { "field1": "foo" }
          },
          "big": {
            "span_near": {
              "clauses": [
                { "span_term": { "field1": "bar" } },
                { "span_term": { "field1": "baz" } }
              ],
              "slop": 5,
              "in_order": true
            }
          }
        }
      }
    }
    ```

    

- Specialized queries

  - Distance feature

    ```
    
    PUT /items
    {
      "mappings": {
        "properties": {
          "name": {
            "type": "keyword"
          },
          "production_date": {
            "type": "date"
          },
          "location": {
            "type": "geo_point"
          }
        }
      }
    }
    
    ```

    ```
    PUT /items/_doc/1?refresh
    {
      "name" : "chocolate",
      "production_date": "2018-02-01",
      "location": [-71.34, 41.12]
    }
    
    ```

    

  - More like this

    ```
    GET /_search
    {
      "query": {
        "more_like_this": {
          "fields": [ "title", "description" ],
          "like": [
            {
              "_index": "imdb",
              "_id": "1"
            },
            {
              "_index": "imdb",
              "_id": "2"
            },
            "and potentially some more text here as well"
          ],
          "min_term_freq": 1,
          "max_query_terms": 12
        }
      }
    }
    ```

    

  - Percolate

    ```
    PUT /my-index-00001
    {
      "mappings": {
        "properties": {
          "message": {
            "type": "text"
          },
          "query": {
            "type": "percolator"
          }
        }
      }
    }
    ```

    

  - Rank feature

    - field
    - boost
    - saturation
    - log
    - sigmoid
    - linear

    ```
    PUT /test
    {
      "mappings": {
        "properties": {
          "pagerank": {
            "type": "rank_feature"
          },
          "url_length": {
            "type": "rank_feature",
            "positive_score_impact": false
          },
          "topics": {
            "type": "rank_features"
          }
        }
      }
    }
    ```

    

  - Script

    ```
    GET /_search
    {
      "query": {
        "bool": {
          "filter": {
            "script": {
              "script": """
                double amount = doc['amount'].value;
                if (doc['type'].value == 'expense') {
                  amount *= -1;
                }
                return amount < 10;
              """
            }
          }
        }
      }
    }
    ```

    

  - Script score

    - query
    - script
    - min_score
    - boost

    ```
    GET /_search
    {
      "query": {
        "script_score": {
          "query": {
            "match": { "message": "elasticsearch" }
          },
          "script": {
            "source": "doc['my-int'].value / 10 "
          }
        }
      }
    }
    ```

    

  - Wrapper

    ```
    GET /_search
    {
      "query": {
        "wrapper": {
          "query": "eyJ0ZXJtIiA6IHsgInVzZXIuaWQiIDogImtpbWNoeSIgfX0=" 
        }
      }
    }
    ```

    

  - Pinned Query

    - ids
    - docs
    - organic

    ```
    GET /_search
    {
      "query": {
        "pinned": {
          "ids": [ "1", "4", "100" ],
          "organic": {
            "match": {
              "description": "iphone"
            }
          }
        }
      }
    }
    ```

    

- Term-level queries

  - Exists

    ```
    GET /_search
    {
      "query": {
        "exists": {
          "field": "user"
        }
      }
    }
    ```

    

  - Fuzzy

    - value
    - fuzziness
    - max_expansions
    - prefix_length
    - transpositions
    - rewrite

    ```
    GET /_search
    {
      "query": {
        "fuzzy": {
          "user.id": {
            "value": "ki"
          }
        }
      }
    }
    ```

    

  - IDs
    
  - Prefix

  - Range

  - Regexp

  - Term

  - Terms

  - Terms set

  - Type Query

  - Wildcard

- minimum_should_match parameter

  - Integer
  - Negative integer
  - Percentage
  - Negative percentage
  - Combination
  - Multiple combinations

- rewrite parameter

  - fuzzy
  - prefix
  - query_string
  - regexp
  - wildcard

- Regular expression syntax

  - regexp
  - query_string

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

https://kknews.cc/zh-tw/code/pb3rvpp.html