# Elasticsearch

## 大綱

**大綱上要跟每個標題、項目符號的文字相同**

1. Elasticsearch vs solr

2. Elasticsearch  比對 MySQL

3. Elasticsearch Index介紹

   3.1index組成

   - data
     - 單個document 
       - index
       - get
       - post
       - update
     - 多個document 
       - Multi get
       - Bulk
       - Delete by query
       - Update by query
       - Reindex
   - mapping
   - setting

   3.2 field-data Type

   - metadata field
     - _index
     - _type
     - _id
     - _scource
     - _size
     - _doc_count
   - data type
     - common types
       - binary
       - boolean
       - Keywords
       - Numbers
       - Dates
       - alias
     - Objects and relational types
       - Object
       - flattened
       - nested
       - join
     - Structured data types
       - Range
       - ip
       - version
       - murmur3
     - Aggregate data types
       - aggregate_metric_double
       - histogram
     - Text search types
       - text fields
       - annotated-text
       - completion
       - search_as_you_type
       - token_count
     - Document ranking types
       - dense_vector
       - sparse_vector
       - rank_feature
       - rank_features
     - Spatial data types
       - geo_point
       - geo_shape
       - point
       - shape
     - 

   3.3.analyzer

   - 原理
   - 如何使用iK分詞
   - 同義詞、分詞字點檔設定/使用

4. Query DSL介紹

   4.1 查詢語法

   4.2 排序

   4.3 高量

5. 用laravel 如何Query Elasticsearch 取得Data

### 1. Elasticsearch vs solr

什麼是solr?

- Solr是Apache Solr基於java開源搜尋引擎Lucene，Lucene更多的是一個軟體包，還不能稱之為搜尋引擎，而solr則完成對lucene的封裝，是一個真正意義上的搜尋引擎框架。

- 具有類似REST的API。您通過JSON，XML，CSV或二進位制檔案將文件放入其中（稱為“索引”）。您可以通過HTTP GET查詢它並接收JSON，XML，CSV或二進位制結果。

- solr特點

  - 全文索引
  - 高亮
  - 分面搜索
  - 實時索引
  - 動態聚類
  - 資料庫集成
  - NoSQL特性和豐富的文檔處理（例如Word和PDF文件）

| 項目 | Elasticsearch  | solr |
| ---- | -------------- | ---- |
| 格式 | xml、csv、json | json |
|      |                |      |
|      |                |      |



### 2. Elasticsearch  比對 MySQL

| 項目     | Elasticsearch | MySQL               |
| -------- | ------------- | ------------------- |
| 數據庫   | Index         | DB                  |
| 表格     | Type          | Table               |
| 列       | Document      | Row                 |
| 欄位     | Field         | Column              |
| 資料架構 | Mapping       | Schema              |
| 查詢     | GET..         | Select * From table |
| 更新     | POST          | Update table Set    |

### 3. Elasticsearch Index介紹

elasticsearch可以用索引值來做資料搜尋，有利於程式的logs或數據做篩選，Elasticsearch 建立每一筆資料時，都以JSON document方式呈現

- 定義 index 中每個 term 的名稱

- 定義每個 term 的資料型態，例如：string、Interger、boolean

- term & inverted index 的相關配置 (要使用哪個 Analyzer，或是不被索引)

- 包含多個 Key/Value 組合，就像是資料庫中的一筆資料
- 但跟資料庫不一樣的是，JSON 格式靈活不受限，不須預先定義格式
- 每個 Key/Value 的類型(string, number, boolean … etc) 可以自己指定或是由 Elasticsearch 幫忙推算

term是指field的單位，跟match的用意是一樣，兩種都是做Query用的，差別在於match可以對field做分詞查詢，但是term不行

#### 3.1 Index組成

在Kibnna裡，會看到每筆資料都會有index，每筆index都有記錄，可以存放哪些欄位以及搜尋設定，index有3個部分組成

- data：由document 組成，使用index、get、post、update等等指令，實現CURD

  - 單個document 
    - index
    - get
    - post
    - update
  - 多個document 
    - Multi get
    - Bulk
    - Delete by query
    - Update by query
    - Reindex

  每個document，都會存放index、type、id、 version等等資訊，來定義index的資訊。

  去找官方文件找index怎麼組成的，裡面會有index、body、mapping等等，會跟程式碼建立index內容差不多
  
  ```json
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

- mapping：設定索引的類型與搜尋範圍

  每個index的資料都用放在mapping，記錄欄位的類型

  - _doc：索引值每個文檔
  - properties：設定文檔的配置
  - type：設定文檔的欄位是什麼類型
  - fields：設定欄位要用什麼屬性
  - ignore_above：限制欄位長度

  ```json
  //索引值的mapping中有my_id、my_join_field、pass-grades、student、text，每個欄位限制長度為256
  {
    "mappings": {
      "_doc": {
        "properties": {
          "my_id": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "my_join_field": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "pass-grades": {
            "type": "boolean"
          },
          "student": {
            "type": "alias",
            "path": "pass-grades"
          },
          "text": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          }
        }
      }
    }
  }
  ```

- setting：設定篩選

  定義inedx的欄位要存放多少資料，做篩選

  - routing
  - allocation
  - include
  - _tier_preference
  - number_of_shards
  
  ```json
  {
    "settings": {
      "index": {
        "routing": {
          "allocation": {
            "include": {
              "_tier_preference": "data_content"
            }
          }
        },
        "number_of_shards": "1",
        "provided_name": "my-index1",
        "creation_date": "1638069278307",
        "number_of_replicas": "1",
        "uuid": "GIgVgQpxT2CCdcUSP1H6QA",
        "version": {
          "created": "7150199"
        }
      }
    },
    "defaults": {
      "index": {
        "flush_after_merge": "512mb",
        "final_pipeline": "_none",
        "max_inner_result_window": "100",
        "unassigned": {
          "node_left": {
            "delayed_timeout": "1m"
          }
        },
        "max_terms_count": "65536",
        "rollup": {
          "source": {
            "name": "",
            "uuid": ""
          }
        },
        "lifecycle": {
          "name": "",
          "parse_origination_date": "false",
          "step": {
            "wait_time_threshold": "12h"
          },
          "indexing_complete": "false",
          "rollover_alias": "",
          "origination_date": "-1"
        },
        "routing_partition_size": "1",
        "force_memory_term_dictionary": "false",
        "max_docvalue_fields_search": "100",
        "merge": {
          "scheduler": {
            "max_thread_count": "2",
            "auto_throttle": "true",
            "max_merge_count": "7"
          },
          "policy": {
            "floor_segment": "2mb",
            "max_merge_at_once_explicit": "30",
            "max_merge_at_once": "10",
            "max_merged_segment": "5gb",
            "expunge_deletes_allowed": "10.0",
            "segments_per_tier": "10.0",
            "deletes_pct_allowed": "33.0"
          }
        },
        "max_refresh_listeners": "1000",
        "max_regex_length": "1000",
        ....
  ```

#### 3.2 field Data Type

##### metadata field

- _index

  索引值，可以當我每筆資料的ID，如果索引值需要做多筆查詢時，有時需要添加索引相關的子查詢。

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

  存放資料型態，**這裡要注意6.0以上就棄用了**

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

  唯一值

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

  存放資料

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

  檔案大小

- _doc_count

  doc有多少筆資料

##### 	data type

- common types(常見類型)

  - binary

    設定欄位為字符編碼，但是要符合`Base64` 編碼

    ```json
    //設定blob欄位設定資料型態為binary
    PUT my-index-000001
    {
      "mappings": {
        "properties": {
          "blob": {
            "type": "binary"
          }
        }
      }
    }
    
    //更新my-index-000001，id為1的資料，將blob設定為U29tZSBiaW5hcnkgYmxvYg
    PUT my-index-000001/_doc/1
    {
      "blob": "U29tZSBiaW5hcnkgYmxvYg" 
    }
    
    -----------------------------------------------
    
    //例如：binary存放token
    PUT my-index1
    {
      "mappings": {
        "properties": {
          "token": {
            "type": "binary"
          }
        }
      }
    }
    
    PUT my-index1/_doc/1
    {
      "token": "eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwiYWNjb3VudCI6InVzZXIxIiwidXNlck5hbWUiOiLnjovkupQiLCJkZXB0bm8iOiI2MzExMDIiLCJhdXRod2F5IjoiMSIsImNyZWF0ZUF0IjoxNjE0OTM4MTE0OTc1LCJleHBpcmVBdCI6MTYxNDk0ODkxNDk3NSwiaWF0IjoxNjE0OTM4MTE0LCJleHAiOjE2MTQ5NDg5MTR9.uyy7NiUzM7TcKbt5hakF-b-foJpEvINQ0J2nDrqtpwI" 
    }
    ```

  - boolean

    設定欄位為布林值

    ```json
    //設定is_published欄位設定資料型態為boolean
    PUT my-index-000001
    {
      "mappings": {
        "properties": {
          "is_published": {
            "type": "boolean"
          }
        }
      }
    }
    
    //更新my-index-000001的doc，id為1的資料，將is_published設定為true
    POST my-index-000001/_doc/1
    {
      "is_published": "true" 
    }
    
    -----------------------------------------------
    
    //例如：boolean來記錄是否成功
    PUT my-index1
    {
      "mappings": {
        "properties": {
          "is_success": {
            "type": "boolean"
          }
        }
      }
    }
    
    POST my-index1/_doc/1
    {
      "is_success": "true" 
    }
    ```

  - keywords

    設定欄位為關鍵字，使用keyword有分三種

    1. 設定關鍵字資料型態

       ```json
       //設定tags欄位設定資料型態為keyword
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

    2. 設定常用的關鍵字資料

       這裡要注意的是，一旦用constant_keyword，就無法將constant_keyword設定別的value，因為已經被constant_keyword當作是索引的關鍵字搜尋

       ```json
       //設定timestam欄位為date，message欄位為text，level欄位為constant_keyword，常用關鍵字，value為debug
       //level已經被設定為關鍵字搜尋
       PUT logs-debug
       {
         "mappings": {
           "properties": {
             "@timestamp": {
               "type": "date"
             },
             "message": {
               "type": "text"
             },
             "level": {
               "type": "constant_keyword",
               "value": "debug"
             }
           }
         }
       }
       
       //兩種POST都可以設定
       POST logs-debug/_doc
       {
         "date": "2019-12-12",
         "message": "Starting up Elasticsearch",
         "level": "debug"
       }
       
       POST logs-debug/_doc
       {
         "date": "2019-12-12",
         "message": "Starting up Elasticsearch"
       }
       
       //用level欄位找value為debug,所有的doc資料，這時上面兩種資料都會查的到
       GET logs-debug/_search
       {
         "query": {
           "match": {
             "level": "debug"
           }
         }
       }
       
       -----------------------------------------------
       
       //例如：用article當作關鍵字搜尋，去找已經出版的文章
       PUT published-articles
       {
         "mappings": {
           "properties": {
             "title": {
               "type": "text"
             },
             "content": {
               "type": "text"
             },
             "published": {
               "type": "constant_keyword",
               "value": "article"
             }
           }
         }
       }
       
       //新增兩篇已出版的文章
       POST published-articles/_doc
       {
         "title": "我的第一篇文章",
         "message": "xasd12323fsdf"
       }
       
       POST published-articles/_doc
       {
         "title": "我的第二篇文章",
         "message": "aaabbbcccddd"
       }
       
       //這時用索引值published-articles，去找全部已出版文章資料
       GET published-articles/_search
       {
         "query": {
           "match": {
             "published": "article"
           }
         }
       }
       ```

    3. 設定萬用字元的資料型態

       做法有點像正規化表示法，在搜尋時可以指定某個欄位做正規化查詢，但前提是要先欄位的type設定為wildcard

       ```json
       //設定my_wildcard做萬用字元
       PUT my-index-000001
       {
         "mappings": {
           "properties": {
             "my_wildcard": {
               "type": "wildcard"
             }
           }
         }
       }
       
       //更新my-index-000001的doc，id為1的資料，將my_wildcard設定文字
       PUT my-index-000001/_doc/1
       {
         "my_wildcard" : "This string can be quite lengthy"
       }
       
       //查詢索引值my-index-000001的萬用字元欄位my_wildcard，去找有包含quite和lengthy的文字
       GET my-index-000001/_search
       {
         "query": {
           "wildcard": {
             "my_wildcard": {
               "value": "*quite*lengthy"
             }
           }
         }
       }
       
       -----------------------------------------------
       
       //例如：索引值my-index1，找出Apple相關的文字資料
       GET my-index1/_search
       {
         "query": {
           "wildcard": {
             "my_wildcard": {
               "value": "Apple*"
             }
           }
         }
       }
       ```

  - numbers

    以下皆為數值的資料型態

    - long
    - integer
    - short
    - byte
    - double
    - float
    - half_float：16進位的浮點數
    - scaled_float：16進位的浮點數
    - unsigned_long

    ```json
    //設定number_of_bytes欄位設定資料型態為integer、time_in_seconds欄位設定資料型態為float、price欄位設定資料型態為scaled_float
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
    
    -----------------------------------------------
    
    //例如：設定學生姓名與成績
    PUT my-index1
    {
      "mappings": {
        "properties": {
          "student": {
            "type": "text"
          },
          "score": {
            "type": "integer"
          }
        }
      }
    }
    
    ```

  - dates

    設定日期資料型態

  - alias

    設定別的欄位做子查詢，將索引值的doc用別的欄位來當查詢條件，這要注意的是查詢時要用_field_caps，在設定子查詢欄位名稱去搜尋，欄位名稱可以用正規化表示法

    ```json
    //設定route_length_miles的距離當作查詢條件，
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
    
    //在索引值trips，去找欄位有包含route_或transit_mode的資料
    GET trips/_field_caps?fields=route_*,transit_mode
    
    -----------------------------------------------
    
    //例如：索引值及格分數，去找跟學生相關的欄位資料
    GET pass-grades/_field_caps?fields=student*
    
    ```

- Obejct  and relational types(物件與關係類型)

  - Object

    設定索引的資料型態或value

    參數

    - dynamic：禁止動態查詢，如果不想被查詢到就寫false
    - enabled：設定欄位資料被禁止使用，但是要寫false
    - properties：設定索引值mapping的內容

    ```json
    //設定Josh Smith的個人資料，年齡30，住在美國
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
    ----------------------------
    
    //例如：設定Kai Lin的個人資料，住在台灣
    PUT my-index1/_doc/1
    { 
      "region": "TW",
      "manager": { 
        "name": { 
          "first": "Lin",
          "last":  "Kai"
        }
      }
    }
    ```

    

  - flattened

    如果欄位有包含很多層資料時，一般的做法是針對欄位用動態查詢，但是可以用flattened，這種做法好處是如果不知道資料型別，可以用欄位的某個部份資料做搜尋

    參數

    - boost
    - depth_limit
    - doc_values
    - eager_global_ordinals
    - ignore_above
    - index
    - index_options
    - null_value
    - similarity
    - split_queries_on_whitespace

    ```json
    //設定title為text，labels為flattened
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
    
    //設定title與labels資料
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
    
    //查詢labels的資料，兩種方式都可以查的到
    POST bug_reports/_search
    {
      "query": {
        "term": {"labels": "urgent"}
      }
    }
    
    POST bug_reports/_search
    {
      "query": {
        "term": {"labels.release": "v1.3.0"}
      }
    }
    
    ----------------------------
    
    //例如：查詢文章的標題為我的第一篇文章
    POST my_article/_search
    {
      "query": {
        "term": {"article.title": "我的第一篇文章"}
      }
    }
    
    ```

    

  - nested

    設定巢狀資料型態，可以在欄位設定很多層資料

    參數

    - dynamic
    - properties
    - include_in_parent
    - include_in_root

    ```json
    //設定user使用巢狀資料
    PUT my-index-000001
    {
      "mappings": {
        "properties": {
          "user": {
            "type": "nested" 
          }
        }
      }
    }
    
    //設定user多層資料
    PUT my-index-000001/_doc/1
    {
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
    
    //查詢索引值my-index-000001，找出user first欄位為Alice，user last欄位為Smith
    GET my-index-000001/_search
    {
      "query": {
        "bool": {
          "must": [
            { "match": { "user.first": "Alice" }},
            { "match": { "user.last":  "Smith" }}
          ]
        }
      }
    }
    
    ----------------------------
    
    //例如：查詢會員資料，姓名為王小明，帳號為test123434
    GET member-data/_search
    {
      "query": {
        "bool": {
          "must": [
            { "match": { "member.name": "王小明" }},
            { "match": { "member.account":  "test123434" }}
          ]
        }
      }
    }
    ```

    

  - join

    設定欄位做父子類別

    ```json
    //設定my_id為keyword，my_join_field設定question與answer為父子類別
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
    
    //更新my-index-000001的doc，id為1的資料，將my_id設定為1，text設定文字內容，my_join_field設定父類別名稱question
    PUT my-index-000001/_doc/1
    {
      "my_id": "1",
      "text": "This is a question",
      "my_join_field": {
        "name": "question" 
      }
    }
    
    //查詢子類別answer，id為1的資料
    GET my-index-000001/_search
    {
      "query": {
        "parent_id": { 
          "type": "answer",
          "id": "1"
        }
      }
    }
    
    ----------------------------
    
    GET my-index1/_search
    {
        
    }
    ```

    

- Structured data types(結構話資料類型)

  - range

    設定資料型態範圍，每一個資料型態可以設定，如果要查詢資料範圍，有另一種寫法

    - query

      查詢時可以大於、小於當條件，用時間的話，可以加時區

      - gt：大於
      - gte：大於等於
      - lt：小於
      - lte：小於等於
      - time_zone：加上時間

      ```json
      //查詢現在至一小時內的資料，並且加上時區8小時
      GET range_index/_search
      {
        "query" : {
          "range" : {
            "date": {
              "time_zone": "+08:00",        
              "gte":"now-1h/h,
              "lt":"now/h"                  
            }
          }
        }
      }
      ```

    - integer_range：搜尋integer資料範圍

    - float_range：搜尋float資料範圍

    - long_range：搜尋long資料範圍

    - double_range：搜尋double資料範圍

    - date_range：搜尋date資料範圍，可以用format來設定日期格式

    - ip_range：搜尋ip範圍

      每種可加參數，來設定更詳細的範圍資料

      - coerce
      - boost
      - index
      - store

    ```json
    //設定expected_attendees搜尋integer資料範圍，time_frame搜尋date資料範圍，日期格式為日期加時間或只有日期
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
            "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd"
          }
        }
      }
    }
    
    //更新my-index-000001的doc，id為1的資料，將expected_attendees資料範圍設定為10-20，time_frame資料範圍設定為2015-10-31 12:00:00至2015-11-01
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
    
    //查詢索引值range_index的expected_attendees資料，是否有包含12
    GET range_index/_search
    {
      "query" : {
        "term" : {
          "expected_attendees" : {
            "value": 12
          }
        }
      }
    }
    
    //查詢索引值range_index的time_frame資料，是否在2015-10-31至2015-11-01範圍內
    GET range_index/_search
    {
      "query" : {
        "range" : {
          "time_frame" : { 
            "gte" : "2015-10-31",
            "lte" : "2015-11-01",
            "relation" : "within" 
          }
        }
      }
    }
    
    ----------------------------
    
    //例如：查詢成績合格的資料
    GET range-grades/_search
    {
      "query" : {
        "range" : {
          "grades" : {
              "get" : 60,
              "lte" : 100,
          }
        }
      }
    }
    
    
    //range_index的ip_allowlist設定為ip_range
    PUT range_index/_mapping
    {
      "properties": {
        "ip_allowlist": {
          "type": "ip_range"
        }
      }
    }
    
    //更新range_index，id為2的資料，ip_allowlist設定為192.168.0.0/16
    PUT range_index/_doc/2
    {
      "ip_allowlist" : "192.168.0.0/16"
    }
    
    ----------------------------
    
    
    
    ```

    

  - ip

    設定IP欄位，可加參數來查詢，縮小範圍

    - boost
    - dimension
    - doc_values
    - ignore_malformed
    - index
    - null_value
    - on_script_error
    - script
    - store

    ```json
    //設定ip_addr為IP
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
    
    //更新my-index-000001，id為1的資料，將ip_addr設定為192.168.1.1
    PUT my-index-000001/_doc/1
    {
      "ip_addr": "192.168.1.1"
    }
    
    //查詢my-index-000001的ip_addr，有192.168.0.0/16的資料
    GET my-index-000001/_search
    {
      "query": {
        "term": {
          "ip_addr": "192.168.0.0/16"
        }
      }
    }
    
    ----------------------------
    
    //例如：索引值my-index查詢本機的IP資料
    GET my-index/_search
    {
        "query": {
         "term": {
           "ip_addr": "127.0.0.1"  
         }   
        }
    }
    ```

    

  - version

    設定版本欄位，多個mapping做版本區分，如果沒使用range、fuzzy做查詢的話，可以用version來做查詢

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
    
    PUT my-index1
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

    mapper-murmur3套件，可以協助查詢

    ```
    sudo bin/elasticsearch-plugin install mapper-murmur3
    ```

    

- Aggregate data types(聚合資料類型)

  - aggregate_metric_double

    設定聚合數值欄位，可以針對欄位做分析

    - min：最小值
    - max：最大值
    - sum：總和
    - value_count：件數
    - avg：平均
    - exists：
    - range
    - term
    - terms

    ```json
    //設定
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

    ```json
    //更新my-index-000001，id為1的資料，將my_text設定為histigram_1，my_histogram有多個value與counts
    PUT my-index-000001/_doc/1
    {
      "my_text" : "histogram_1",
      "my_histogram" : {
          "values" : [0.1, 0.2, 0.3, 0.4, 0.5], 
          "counts" : [3, 7, 23, 12, 6] 
       }
    }
    
    //更新my-index-000001，id為2的資料，將my_text設定為histigram_2，my_histogram有多個value與counts
    PUT my-index-000001/_doc/2
    {
      "my_text" : "histogram_2",
      "my_histogram" : {
          "values" : [0.1, 0.25, 0.35, 0.4, 0.45, 0.5], 
          "counts" : [8, 17, 8, 7, 6, 2] 
       }
    }
    
    //查詢my-index-000001的my_histogram.values的最小值
    POST /my-index-000001/_search?size=0
    {
      "aggs" : {
        "min_histogram" : { "min" : { "field" : "my_histogram.values" } }
      }
    }
    
    //查詢my-index-000001的my_histogram.values的最大值
    POST /my-index-000001/_search?size=0
    {
      "aggs" : {
        "max_histogram" : { "max" : { "field" : "my_histogram.values" } }
      }
    }
    
    //查詢my-index-000001的my_histogram.values的加總值
    POST /my-index-000001/_search?size=0
    {
      "aggs" : {
        "total_histogram" : { "sum" : { "field" : "my_histogram.values" } }
      }
    }
    
    POST /my-index-000001/_search?size=0
    {
      "aggs" : {
        "total_histogram" : { "value_count" : { "field" : "my_histogram.values" } }
      }
    }
    
    POST /my-index-000001/_search?size=0
    {
      "aggs" : {
        "avg_histogram" : { "avg" : { "field" : "my_histogram.values" } }
      }
    }
    
    GET my-index-000001/_search?size=0&filter_path=aggregations
    {
      "aggs": {
        "latency_ranges": {
          "range": {
            "field": "my_histogram.values",
            "ranges": [
              {"to": 2},
              {"from": 2, "to": 3},
              {"from": 3, "to": 10},
              {"from": 10}
            ]
          }
        }
      }
    }
    
    
    PUT my-index1/_doc/
    {
      "my_text" : "histogram_1",
      "my_histogram" : {
          "values" : [0.1, 0.25, 0.35, 0.4, 0.45, 0.5], 
          "counts" : [8, 17, 8, 7, 6, 2] 
       }
    }
    ```

- Text search types(文字搜尋)

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

    ```json
    //索引值my-index-000001設定full_name的type為text
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
    
    //索引值my-index-000001設定my_field的type為text，fielddata參數為true
    PUT my-index-000001/_mapping
    {
      "properties": {
        "my_field": { 
          "type":     "text",
          "fielddata": true
        }
      }
    }
    
    
    
    PUT my-index1
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

    使用mapper-annotated-text套件

    ```
    sudo bin/elasticsearch-plugin install mapper-annotated-text
    ```

    

  - completion

    ```json
    //索引值music，將suggest設定為completion，title為keyword
    PUT music
    {
      "mappings": {
        "properties": {
          "suggest": {
            "type": "completion"
          },
          "title": {
            "type": "keyword"
          }
        }
      }
    }
    
    //更新索引值music，id為1的資料，將suggest設定input為Nevermind、Nirvana，weight為34
    PUT music/_doc/1?refresh
    {
      "suggest" : {
        "input": [ "Nevermind", "Nirvana" ],
        "weight" : 34
      }
    }
    
    //查詢索引值music的suggest欄位
    POST music/_search
    {
      "_source": "suggest",     
      "suggest": {
        "song-suggest": {
          "prefix": "nir",
          "completion": {
            "field": "suggest", 
            "size": 5           
          }
        }
      }
    }
    
    PUT place
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

    ```json
    //索引值my-index-000001，將my_field的類型設定為search_as_you_type
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
    
    PUT my-index1
    {
      "mappings": {
        "properties": {
          "create_date": {
            "type": "date"
          }
        }
      }
    }
    ```

    

  - token_count

    ```json
    //更新my-index-000001，將name設定為text，fields.length設定為token_count，analyzer為standard
    PUT my-index-000001
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
    
    //更新my-index-000001，id為1的資料，將name設定為John Smith
    PUT my-index-000001/_doc/1
    { "name": "John Smith" }
    
    //更新my-index-000001，id為2的資料，將name設定為Rachel Alice Williams
    PUT my-index-000001/_doc/2
    { "name": "Rachel Alice Williams" }
    
    //查詢my-index-000001，所有資料的name欄位，找出有3個字段的資料
    GET my-index-000001/_search
    {
      "query": {
        "term": {
          "name.length": 3 
        }
      }
    }
    
    ----------------------------
    
    //例如查詢my-index-000001，所有資料的name欄位，找出有2個字段的資料
    //這時會查到John Smith的資料
    GET my-index-000001/_search
    {
      "query": {
        "term": {
          "name.length": 2 
        }
      }
    }
    
    
    
    ```
    
    

- Document ranking types(文件排名)

  - dense_vector

    ```json
    //索引值my-index-000001，將my_vector設定為dense_vector，並且只能放3個資料，my_text設定為text
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
    
    //更新my-index-000001，id為1的資料，將my_text設定為text1，my_vector設定為0.5、10、6
    PUT my-index-000001/_doc/1
    {
      "my_text" : "text1",
      "my_vector" : [0.5, 10, 6]
    }
    
    //更新my-index-000001，id為2的資料，將my_text設定為text2，my_vector設定為-0.5、10、10
    PUT my-index-000001/_doc/2
    {
      "my_text" : "text2",
      "my_vector" : [-0.5, 10, 10]
    }
    
    ```

    

  - sparse_vector

    ```json
    //索引值my-index-000001，將my_vector設定為sparse_vector，my_text設定為keyword
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
    
    //更新my-index-000001，id為1的資料，將my_text設定為text1，my_vector設定一組陣列
    PUT my-index-000001/_doc/1
    {
      "my_text" : "text1",
      "my_vector" : {"1": 0.5, "5": -0.5,  "100": 1}
    }
    
    //更新my-index-000001，id為2的資料，將my_text設定為text2，my_vector設定一組陣列
    PUT my-index-000001/_doc/2
    {
      "my_text" : "text2",
      "my_vector" : {"103": 0.5, "4": -0.5,  "5": 1, "11" : 1.2}
    }
    
    GET my-index-000001/_search
    {
        "query" : {
            "sparse_vector" : {
                "field" : "sparse_vector"
            }
        }
    }
    
    
    ```

    

  - rank_features

    ```json
    //索引值my-index-000001，設定pagerank的類型為rank_feature，url_length的類型為rank_feature，資料為負數也可以做排名
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
    
    //更新my-index-000001，id為1的資料，pagerank設定為8，url_length設定為22
    PUT my-index-000001/_doc/1
    {
      "pagerank": 8,
      "url_length": 22
    }
    
    //查詢my-index-000001，找rank_feature欄位名稱為pagerank的所有資料
    GET my-index-000001/_search
    {
      "query": {
        "rank_feature": {
          "field": "pagerank"
        }
      }
    }
    
    ----------------------------
    
    //例如查詢成績排名的資料
    GET my-index1/_search
    {
      "query": {
        "rank_feature": {
          "field": "scoreRank"
        }
      }
    }
    ```

    

- Spatial data types(空間資料類型)

  - geo_point

    ```json
    //索引值my-index-000001，location的類型為geo_point
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
    
    //更新my-index-000001，id為1的資料，location.lat為41.12，location.lon為-71.34
    PUT my-index-000001/_doc/1
    {
      "text": "Geopoint as an object",
      "location": { 
        "lat": 41.12,
        "lon": -71.34
      }
    }
    
    //更新my-index-000001，id為2的資料，text為Geopoint as a string，location為41.12,-71.34
    PUT my-index-000001/_doc/2
    {
      "text": "Geopoint as a string",
      "location": "41.12,-71.34" 
    }
    
    //更新my-index-000001，id為3的資料，text為Geopoint as a geohash，location為drm3btev3e86
    PUT my-index-000001/_doc/3
    {
      "text": "Geopoint as a geohash",
      "location": "drm3btev3e86" 
    }
    
    //更新my-index-000001，id為4的資料，text為Geopoint as an array，location為[ -71.34, 41.12 ]
    PUT my-index-000001/_doc/4
    {
      "text": "Geopoint as an array",
      "location": [ -71.34, 41.12 ] 
    }
    
    //更新my-index-000001，id為5的資料，text為Geopoint as a WKT POINT primitive，location為POINT (-71.34 41.12)
    PUT my-index-000001/_doc/5
    {
      "text": "Geopoint as a WKT POINT primitive",
      "location" : "POINT (-71.34 41.12)" 
    }
    
    //查詢my-index-000001
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

    ```json
    //索引值my-index-000001，設定location的類型為geo_shape
    PUT my-index-000001
    {
      "mappings": {
        "properties": {
          "location": {
            "type": "geo_shape"
          }
        }
      }
    }
    ```

    - point

      ```json
      //更新my-index-000001，id為1的資料，location的類型為point，coordinates為[-77.03653, 38.897676]
      POST my-index-000001/_doc/1
      {
        "location" : {
          "type" : "point",
          "coordinates" : [-77.03653, 38.897676]
        }
      }
      
      //更新my-index-000001，id為2的資料，location為POINT (-77.03653 38.897676)
      POST my-index-000001/_doc/2
      {
        "location" : "POINT (-77.03653 38.897676)"
      }
      
      //更新my-index-000001，id為3的資料，location的類型為linestring
      POST my-index-000001/_doc/3
      {
        "location" : {
          "type" : "linestring",
          "coordinates" : [[-77.03653, 38.897676], [-77.009051, 38.889939]]
        }
      }
      
      //更新my-index-000001，id為4的資料，location為LINESTRING (-77.03653 38.897676, -77.009051 38.889939)
      POST my-index-000001/_doc/4
      {
        "location" : "LINESTRING (-77.03653 38.897676, -77.009051 38.889939)"
      }
      ```

    - polygon

      ```json
      //更新my-index-000001，id為5的資料，location的類型為polygon，coordinates為[ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ] ]
      POST my-index-000001/_doc/5
      {
        "location" : {
          "type" : "polygon",
          "coordinates" : [
            [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]
          ]
        }
      }
      
      //更新my-index-000001，id為6的資料，location為POLYGON ((100.0 0.0, 101.0 0.0, 101.0 1.0, 100.0 1.0, 100.0 0.0))
      POST my-index-000001/_doc/6
      {
        "location" : "POLYGON ((100.0 0.0, 101.0 0.0, 101.0 1.0, 100.0 1.0, 100.0 0.0))"
      }
      
      //更新my-index-000001，id為7的資料，location的類型為polygon，coordinates為[ [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ], [ [100.2, 0.2], [100.8, 0.2], [100.8, 0.8], [100.2, 0.8], [100.2, 0.2] ] ]
      POST my-index-000001/_doc/7
      {
        "location" : {
          "type" : "polygon",
          "coordinates" : [
            [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ],
            [ [100.2, 0.2], [100.8, 0.2], [100.8, 0.8], [100.2, 0.8], [100.2, 0.2] ]
          ]
        }
      }
      
      //更新my-index-000001，id為8的資料，location為POLYGON ((100.0 0.0, 101.0 0.0, 101.0 1.0, 100.0 1.0, 100.0 0.0))
      POST my-index-000001/_doc/8
      {
        "location" : "POLYGON ((100.0 0.0, 101.0 0.0, 101.0 1.0, 100.0 1.0, 100.0 0.0), (100.2 0.2, 100.8 0.2, 100.8 0.8, 100.2 0.8, 100.2 0.2))"
      }
      ```

      

  - point

    ```json
    //索引值my-index-000001，設定location的類型為point
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
    
    //更新my-index-000001，id為1的資料，text為Point as an object，location.x為41.12，location.y為-71.34
    PUT my-index-000001/_doc/1
    {
      "text": "Point as an object",
      "location": { 
        "x": 41.12,
        "y": -71.34
      }
    }
    
    //更新my-index-000001，id為2的資料，text為Point as a string，location為41.12,-71.34
    PUT my-index-000001/_doc/2
    {
      "text": "Point as a string",
      "location": "41.12,-71.34" 
    }
    
    //更新my-index-000001，id為4的資料，text為Point as array，location為[41.12, -71.34]
    PUT my-index-000001/_doc/4
    {
      "text": "Point as an array",
      "location": [41.12, -71.34] 
    }
    
    //更新my-index-000001，id為5的資料，text為Point as a WKT POINT primitive，location為POINT (41.12 -71.34)
    PUT my-index-000001/_doc/5
    {
      "text": "Point as a WKT POINT primitive",
      "location" : "POINT (41.12 -71.34)" 
    }
    ```

    

  - shape

    ```json
    //索引值my-index-000001，設定location的類型為shape
    PUT my-index-000001
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

    - Point

      ```json
      POST /example/_doc
      {
        "location" : {
          "type" : "point",
          "coordinates" : [-377.03653, 389.897676]
        }
      }
      
      POST /example/_doc
      {
        "location" : "POINT (-377.03653 389.897676)"
      }
      ```

    - LineString

      ```json
      POST /example/_doc
      {
        "location" : {
          "type" : "linestring",
          "coordinates" : [[-377.03653, 389.897676], [-377.009051, 389.889939]]
        }
      }
      
      POST /example/_doc
      {
        "location" : "LINESTRING (-377.03653 389.897676, -377.009051 389.889939)"
      }
      ```

    - polygon

      ```json
      POST /example/_doc
      {
        "location" : {
          "type" : "polygon",
          "coordinates" : [
            [ [1000.0, -1001.0], [1001.0, -1001.0], [1001.0, -1000.0], [1000.0, -1000.0], [1000.0, -1001.0] ]
          ]
        }
      }
      
      POST /example/_doc
      {
        "location" : "POLYGON ((1000.0 -1001.0, 1001.0 -1001.0, 1001.0 -1000.0, 1000.0 -1000.0, 1000.0 -1001.0))"
      }
      
      POST /example/_doc
      {
        "location" : {
          "type" : "polygon",
          "coordinates" : [
            [ [1000.0, -1001.0], [1001.0, -1001.0], [1001.0, -1000.0], [1000.0, -1000.0], [1000.0, -1001.0] ],
            [ [1000.2, -1001.2], [1000.8, -1001.2], [1000.8, -1001.8], [1000.2, -1001.8], [1000.2, -1001.2] ]
          ]
        }
      }
      
      POST /example/_doc
      {
        "location" : "POLYGON ((1000.0 1000.0, 1001.0 1000.0, 1001.0 1001.0, 1000.0 1001.0, 1000.0 1000.0), (1000.2 1000.2, 1000.8 1000.2, 1000.8 1000.8, 1000.2 1000.8, 1000.2 1000.2))"
      }
      
      POST /example/_doc
      {
        "location" : {
          "type" : "polygon",
          "orientation" : "clockwise",
          "coordinates" : [
            [ [1000.0, 1000.0], [1000.0, 1001.0], [1001.0, 1001.0], [1001.0, 1000.0], [1000.0, 1000.0] ]
          ]
        }
      }
      ```

    - MultPoint

      ```json
      POST /example/_doc
      {
        "location" : {
          "type" : "multipoint",
          "coordinates" : [
            [1002.0, 1002.0], [1003.0, 2000.0]
          ]
        }
      }
      
      POST /example/_doc
      {
        "location" : "MULTIPOINT (1002.0 2000.0, 1003.0 2000.0)"
      }
      ```

      

- Other type

  - percolator

    由官方的Query DSL介紹

#### 3.3 analyzer

##### 	原理

analyzer是專門處理分詞的組件，由三個部份組成

- Character Filter：針對原始文件進行處理，例如：去除 HTML tag
- Tokenizer：按照規則進行分詞處理
- Token Filter：將分詞進行加工，例如：過濾分詞、大寫轉小寫、增加同義詞、合併同義詞等等

Elasticsearch 內建了 3 種字元過濾器、10 種分詞器和 31 種詞元過濾器。此外，還可以通過外掛機制獲取第三方實現的相應元件。開發者可以按照自身需求客製化 Analyzer 的組成部分。

ElasticSearch 預設的分詞器並不是處理中文分詞的最優選擇，目前業界主要使用 ik 進行中文分詞。

##### 	如何使用iK分詞器

ik是目前較為主流的 ElasticSearch 開源中文分詞元件，它內建了基礎的中文詞庫和分詞演演算法幫忙開發者快速構建中文分詞和搜尋功能，它還提供了擴充套件詞庫字典和遠端字典等功能，方便開發者擴充網路新詞或流行語。

ElasticSearch 官網有提供ik分詞器的[下載點](https://github.com/medcl/elasticsearch-analysis-ik)，這裡要注意下載前，**ik的版本要跟ElasticSearch一樣**，下載完之後，在ElasticSearch 目錄的plugins，建立ik資料夾，解壓縮後放在ik資料夾裡面

另一種方法是使用指令下載，開cmd到ElasticSearch/bin目錄底下，/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.15.1/elasticsearch-analysis-ik-7.15.1.zip

解壓縮之後，重新啟動ElasticSearch 會看到analysis-ik

![elasticDevTool4](C:\xampp\htdocs\markdown_note\assets\images\elasticDevTool4.png)

ik有提供3種內建詞典分別是：

- main.dic：主詞典，包括日常的通用詞語，比如程式設計師和程式設計等；
- quantifier.dic：量詞詞典，包括日常的量詞，比如公分、公尺和小時等；
- stopword.dic：停用詞，主要指英語的停用詞，比如 a、what、that 等。

另外，使用者可以在ik/config/IKAnalyzer.cfg.xml做修改，自行更換自訂的dic

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
  <comment>IK Analyzer 擴充套件設定</comment>
  <!--使用者可以在這裡設定自己的擴充套件字典 -->
  <entry key="ext_dict">custom/mydict.dic</entry>
   <!--使用者可以在這裡設定自己的擴充套件停止詞字典-->
  <entry key="ext_stopwords">custom/ext_stopword.dic</entry>
   <!--使用者可以在這裡設定遠端擴充套件字典 -->
  <entry key="remote_ext_dict">location</entry>
   <!--使用者可以在這裡設定遠端擴充套件停止詞字典-->
  <entry key="remote_ext_stopwords">http://xxx.com/xxx.dic</entry>
</properties>
```

##### 	分詞、同義詞字典檔設定/使用

- 分詞

  在ElasticSearch目錄的`plugins\ik\config\IKAnalyzer.cfg.xml`，將`key="ext_dict"`套用的dic檔案設定分詞，雖然ik的my.dic有設定分詞，但是全都是簡體字，如果要設定繁體字，要自行建立

  例如：蘋果、手機、蘋果手機

  ```txt
  蘋果
  手機
  蘋果手機
  ```

  ```json
  //可以用兩種查詢查中文分詞，一種是找出蘋果手機這個分詞，另一種找出蘋果手機的全部分詞
  POST _analyze
  {
    "text": "蘋果手機",
    "analyzer": "ik_smart"
  }
  
  {
    "tokens" : [
      {
        "token" : "蘋果手機",
        "start_offset" : 0,
        "end_offset" : 4,
        "type" : "CN_WORD",
        "position" : 0
      }
    ]
  }
  
  ----------------------------
  
  POST _analyze
  {
    "text": "蘋果手機",
    "analyzer": "ik_max_word"
  }
  
  {
    "tokens" : [
      {
        "token" : "蘋果手機",
        "start_offset" : 0,
        "end_offset" : 4,
        "type" : "CN_WORD",
        "position" : 0
      },
      {
        "token" : "蘋果",
        "start_offset" : 0,
        "end_offset" : 2,
        "type" : "CN_WORD",
        "position" : 1
      },
      {
        "token" : "手機",
        "start_offset" : 2,
        "end_offset" : 4,
        "type" : "CN_WORD",
        "position" : 2
      }
    ]
  }
  
  ----------------------------
  
  //如果沒設定的分詞的話，系統會把中文字一個一個拆開，每個都是視為分詞
  POST _analyze
  {
    "text": "安卓手雞",
    "analyzer": "ik_smart"
  }
  
  {
    "tokens" : [
      {
        "token" : "安",
        "start_offset" : 0,
        "end_offset" : 1,
        "type" : "CN_CHAR",
        "position" : 0
      },
      {
        "token" : "卓",
        "start_offset" : 1,
        "end_offset" : 2,
        "type" : "CN_CHAR",
        "position" : 1
      },
      {
        "token" : "手",
        "start_offset" : 2,
        "end_offset" : 3,
        "type" : "CN_CHAR",
        "position" : 2
      },
      {
        "token" : "雞",
        "start_offset" : 3,
        "end_offset" : 4,
        "type" : "CN_CHAR",
        "position" : 3
      }
    ]
  }
  ```

- 同義詞

  使用同義詞有兩種方法，一種是用ElasticSearch 的Synonym建立，另一種是自訂同義詞

  - Synonym

    在建立索引值時，用synonym設定好同義詞，官網有介紹到synonym_graph

    例如：iPhone與蘋果手機為同義詞

    這裡要注意，範例中使用蘋果手機，必須要先建立好，蘋果手機這個分詞後，在設定同義詞。

    ```json
    //建立索引值test_synonym1
    PUT test_synonym1
    {
      "settings": {
        "analysis": {
          "filter": {
            "my_synonyms": {
              "type": "synonym",
              "synonyms": [
                "iPhone, 蘋果手機"
              ]
            }
          },
          "analyzer": {
            "my_analyzer": {
              "tokenizer": "standard",
              "filter": [
                "my_synonyms"
              ]
            }
          }
        },
        "number_of_shards": 1
      },
      "mappings": {
        "properties": {
          "content": {
            "type": "text",
            "analyzer": "my_analyzer"
          }
        }
      }
    
    //索引值test_synonym的my_analyzer，查詢iPhone
    POST test_synonym1/_analyze
    {
      "text": "iPhone",
      "analyzer": "my_analyzer"
    }
    
    //查詢結果，iPhone與蘋果手機都會顯示
    {
      "tokens" : [
        {
          "token" : "iphone",
          "start_offset" : 4,
          "end_offset" : 10,
          "type" : "<ALPHANUM>",
          "position" : 4
        },
        {
          "token" : "蘋",
          "start_offset" : 4,
          "end_offset" : 10,
          "type" : "SYNONYM",
          "position" : 4
        },
        {
          "token" : "果",
          "start_offset" : 4,
          "end_offset" : 10,
          "type" : "SYNONYM",
          "position" : 5
        },
        {
          "token" : "手",
          "start_offset" : 4,
          "end_offset" : 10,
          "type" : "SYNONYM",
          "position" : 6
        },
        {
          "token" : "機",
          "start_offset" : 4,
          "end_offset" : 10,
          "type" : "SYNONYM",
          "position" : 7
        }
      ...
    ```

    ```json
    //test_synonym1建立一筆資料
    PUT test_synonym1/_doc/1
    {
      "content": "我有一隻蘋果手機"
    }
    
    //查詢索引值test_synonym1，來驗證蘋果手機，這個名詞否為同義詞
    GET test_synonym1/_validate/query?rewrite=true
    {
      "query": {
        "match": {
          "content": "蘋果手機"
        }
      }
    }
    
    //查詢結果，搜尋蘋果手機時，也會搜尋到iphone
    {
      "_shards" : {
        "total" : 1,
        "successful" : 1,
        "failed" : 0
      },
      "valid" : true,
      "explanations" : [
        {
          "index" : "myindex4",
          "valid" : true,
          "explanation" : """content:"蘋 果 手 機" content:iphone"""
        }
      ]
    }
    
    ----------------------------
    //查詢我有一隻iPhone
    GET test_synonym1/_search
    {
      "query": {
        "match": {
          "content": "我有一隻iPhone"
        }
      }
    }
    
    //這時系統會找到，剛建立的我有一隻蘋果手機
    {
        ...
        "hits" : {
        "total" : {
          "value" : 2,
          "relation" : "eq"
        },
        "max_score" : 1.6215926,
        "hits" : [
          {
            "_index" : "myindex4",
            "_type" : "_doc",
            "_id" : "1",
            "_score" : 1.6215926,
            "_source" : {
              "content" : "我有一隻蘋果手機"
            }
          },
         ...
    }
    
    ```

  - 自訂同義詞

    在ElasticSearch 目錄的config/analysis，建立synonym.txt

    例如：iPhone與蘋果手機為同義詞

    ```txt
    iPhone,蘋果手機
    ```

    ```json
    //建立索引值test_synonym2
    PUT test_synonym2
    {
        "settings": {
    		"index" : {
                "analysis" : {
                    "analyzer" : {
                        "synonym" : {
                            "tokenizer" : "whitespace",
                            "filter" : ["my_synonyms"]
                        }
                    },
                    "filter" : {
                        "my_synonyms" : {
                            "type" : "synonym",
                            "synonyms_path" : "analysis/synonym.txt"
                        }
                    }
                }
            }
        }
    }
    
    //test_synonym2建立一筆資料
    PUT test_synonym2/_doc/1
    {
      "content": "我有一隻蘋果手機"
    }
    
    //查詢我有一隻iPhone，這筆資料
    GET test_synonym2/_search
    {
      "query": {
        "match": {
          "content": "我有一隻iPhone"
        }
      }
    }
    
    //這時系統會找到，剛建立的我有一隻蘋果手機
    {
        ...
        "hits" : [
          {
            "_index" : "test_synonym2",
            "_type" : "_doc",
            "_id" : "1",
            "_score" : 1.1507283,
            "_source" : {
              "content" : "我有一隻蘋果手機"
            }
          }
        ]
        ...
    }
    ```

**Mapping與MySQL的Data Type差別**

|      | Mapping | MySQL                                 |
| ---- | ------- | ------------------------------------- |
| 文字 | text    | CHAR、VARCHER、NVARCHER               |
| 日期 | Dates   | DATE、TIME、YEAR、DATETIME、TIMESTAMP |
| 數值 | Numbers | INTEGER、DOUBLE、FLOAT                |
|      |         |                                       |



### 4. Query DSL

#### 	4.1 查詢語法

- Query and filter context

  - Query context(查詢文字)

    用search API在query參數，來查詢mapping裡有符合_score欄位的資料

  - filter context(過濾文字)

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

    可以一個或多個條件，針對某一個欄位做查詢

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

      - after：設定篩選欄位的後面
      - before：設定篩選欄位的前面
      - contained_by：設定篩選欄位的間格
      - containing：設定篩選欄位的間格中的順序
      - not_contained_by：設定不包含篩選欄位的間隔資料
      - not_containing：設定不包含篩選的欄位資料
      - not_overlapping
      - overlapping：設定間隔
      - script：設定腳本

    ```json
    //查詢my favorite food這段文字，後面要接上hot water或cold porridge，有符合條件才會找的到
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
    - minimum_should_match：這裡用到minimum_should_match參數
    - zero_terms_query：
      - none：刪除analyzer所有的標記資料
      - all：回傳analyzer所有的標記資料

    ```json
    //查詢message中，找出this is a test這段文字的資料
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
    //查詢欄位中是否有quick、brown、f，這3種文字
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

    ```json
    //查詢欄位中是否有this is a test，並且確認this is a test文字中是否包含自訂分詞
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

    - query：設定查詢欄位
    - analyzer：設定分詞
    - max_expansions：設定查詢件數，預設50筆
    - slop：
    - zero_terms_query

    ```json
    //查詢欄位中是否有包含quick brown f
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

    - best_fields：可查詢任一種欄位
    - most_fields：可以跟多個欄位做查詢
    - cross_fields：查詢欄位資料中，全部的分詞
    - phrase：使用match_phrase時，查詢符合的資料
    - phrase_prefix：使用match_phrase_prefix時，查詢符合的資料
    - bool_prefix：使用match_bool_prefix時，查詢符合的資料

    ```json
    //查詢subject、message兩個欄位，找出this is a test這段文字
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

    ```json
    //查詢this is bonsai cool設定為常用名詞
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

    - query：搜尋欄位
    - default_field：
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

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    ```json
    GET /_search
    {
      "query": {
        "span_term" : { "user.id" : "kimchy" }
      }
    }
    ```

    

  - Span within

    ```json
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

    ```json
    
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

    ```json
    PUT /items/_doc/1?refresh
    {
      "name" : "chocolate",
      "production_date": "2018-02-01",
      "location": [-71.34, 41.12]
    }
    
    ```

    

  - More like this

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    ```json
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

    ```json
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
    
    ```json
    GET /_search
    {
      "query": {
        "ids" : {
          "values" : ["1", "4", "100"]
        }
      }
    }
    ```
    
    
    
  - Prefix

    ```
    GET /_search
    {
      "query": {
        "prefix": {
          "user.id": {
            "value": "ki"
          }
        }
      }
    }
    ```

    

  - Range

    ```
    GET /_search
    {
      "query": {
        "range": {
          "age": {
            "gte": 10,
            "lte": 20,
            "boost": 2.0
          }
        }
      }
    }
    ```

    

  - Regexp

    ```
    GET /_search
    {
      "query": {
        "regexp": {
          "user.id": {
            "value": "k.*y",
            "flags": "ALL",
            "case_insensitive": true,
            "max_determinized_states": 10000,
            "rewrite": "constant_score"
          }
        }
      }
    }
    ```

    

  - Term

    ```
    GET /_search
    {
      "query": {
        "term": {
          "user.id": {
            "value": "kimchy",
            "boost": 1.0
          }
        }
      }
    }
    ```

    

  - Terms

    ```
    GET /_search
    {
      "query": {
        "terms": {
          "user.id": [ "kimchy", "elkbee" ],
          "boost": 1.0
        }
      }
    }
    ```

    

  - Terms set

    ```
    PUT /job-candidates
    {
      "mappings": {
        "properties": {
          "name": {
            "type": "keyword"
          },
          "programming_languages": {
            "type": "keyword"
          },
          "required_matches": {
            "type": "long"
          }
        }
      }
    }
    ```

    

  - Type Query

    ```
    GET /_search
    {
      "query": {
        "type": {
          "value": "_doc"
        }
      }
    }
    ```

    

  - Wildcard

    ```
    GET /_search
    {
      "query": {
        "wildcard": {
          "user.id": {
            "value": "ki*y",
            "boost": 1.0,
            "rewrite": "constant_score"
          }
        }
      }
    }
    ```

    

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

**Dev Tools**

Elasticsearch中的Kibana有個DevTool可以做設定，可以在這裡進行查詢，在左側寫好資料後，點選三角形圖示，會對應顯示Mapping的資料

![elasticDevTool1](C:\xampp\htdocs\markdown_note\assets\images\elasticDevTool1.png)

#### 4.2 sort排序

- 排序方式
  - asc
  - desc

- 排序模式

  - min
  - max
  - sum
  - avg
  - median

  **排序查詢結果**

  需要用type只能某個欄位做排序

  ```json
  //設定post_date、user、name、age
  PUT /my-index-000001
  {
    "mappings": {
      "properties": {
        "post_date": { "type": "date" },
        "user": {
          "type": "keyword"
        },
        "name": {
          "type": "keyword"
        },
        "age": { "type": "integer" }
      }
    }
  }
  
  //查詢泡菜資料，並且用post_date、user、name、age做排序，post_date設定strict_date_optional_time_nanos參數做由小至大，user沒設定會用預設由小至大做排序，name、age設定由大至小做排序
  GET /my-index-000001/_search
  {
    "sort" : [
      { "post_date" : {"order" : "asc", "format": "strict_date_optional_time_nanos"}},
      "user",
      { "name" : "desc" },
      { "age" : "desc" },
      "_score"
    ],
    "query" : {
      "term" : { "user" : "kimchy" }
    }
  
  ```

  **排序某欄位的值**

  ```json
  //設定巧克力的價錢有20、4
  PUT /my-index-000001/_doc/1?refresh
  {
     "product": "chocolate",
     "price": [20, 4]
  }
  
  //查詢巧克力全部的價錢做平均，在對應價錢與平均值做排序
  POST /_search
  {
     "query" : {
        "term" : { "product" : "chocolate" }
     },
     "sort" : [
        {"price" : {"order" : "asc", "mode" : "avg"}}
     ]
  }
  ```

  **用數值做排序**

  ```json
  //查詢泡菜的發布日期，並且strict_date_optional_time_nanos參數做排序
  GET /my-index-000001/_search
  {
    "sort" : [
      { "post_date" : {"format": "strict_date_optional_time_nanos"}}
    ],
    "query" : {
      "term" : { "user" : "kimchy" }
    }
  }
  ```

  **嵌入排序**

  ```json
  //查詢產品巧克力，價錢與平均值做排序，並且包裝顏色是藍色的
  POST /_search
  {
     "query" : {
        "term" : { "product" : "chocolate" }
     },
     "sort" : [
         {
            "offer.price" : {
               "mode" :  "avg",
               "order" : "asc",
               "nested": {
                  "path": "offer",
                  "filter": {
                     "term" : { "offer.color" : "blue" }
                  }
               }
            }
         }
      ]
  }
  ```

  無法比較的資料做排序

  ```json
  //查詢無法比較巧克力的價錢的資料，並且做排序
  GET /_search
  {
    "sort" : [
      { "price" : {"missing" : "_last"} }
    ],
    "query" : {
      "term" : { "product" : "chocolate" }
    }
  }
  ```

  距離做排序

  ```json
  //使用者jhon的資料有多個距離，針對距離做排序
  GET /_search
  {
    "sort" : [
      {
        "_geo_distance" : {
            "pin.location" : [-70, 40],
            "order" : "asc",
            "unit" : "km",
            "mode" : "min",
            "distance_type" : "arc",
            "ignore_unmapped": true
        }
      }
    ],
    "query" : {
      "term" : { "user" : "jhon" }
    }
  }
  ```

#### 4.3 Highlight高亮

高亮在搜尋指令的資料中，會用顏色來標記查詢到的資料，設定高亮搜尋，只要加上highlight指定某個欄位即可

高亮查詢分為3種，unified、plain、fvh

- unified：通用查詢，例如：fuzzy、regex等等

- plain：高亮的預設值，用顏色來標記查詢到的資料

  ```json
  //例如設定一筆資料
  PUT blog_website/_doc/1
  {
    "title": "我的第一篇博客",
    "content": "大家好，這是我寫的第一篇博客！"
  }
  
  //查詢索引值blog_website，找出title資料為博客，並且做高亮
  GET blog_website/_search
  {
    "query": {
      "match": {
        "title": "博客"
      }
    },
    "highlight": {
      "fields": {
        "title": {
            //可以不寫，plain為預設值
            "type": "plain"
        }
      }
    }
  }
  
  //查詢結果看到title的博客，兩個字用<em>標記起來
  {
     ...
     "hits" : [
        {
          "_index" : "blog_website",
          "_type" : "blogs",
          "_id" : "1",
          "_score" : 0.26706278,
          "_source" : {
            "title" : "我的第一篇博客",
            "content" : "大家好，這是我寫的第一篇博客！"
          },
          "highlight" : {
            "title" : [
              "我的第一篇<em>博</em><em>客</em>"
            ]
          }
        }
      ]
     ...
  }
  ```

- fast vector highlight(fvh)：快速做高亮查詢

  - 適用於欄位有大量資料，可以快速查詢
  - 建立索引值mapping的時候，查詢欄位必須要寫`"term_vector" : "with_positions_offsets"`
  - 可以用matched_fields、Boosting Query做設定

  ```json
  GET /blog_website/blogs/_search
  {
    "query": {
      "match": {
        "title": "博客"
      }
    },
    "highlight": {
      "fields": {
        "title": {
            "type": "fvh"
        }
      }
    }
  }
  ```

- highlight可以做設定

  - boundary_chars：設定邊界字符號，例如：空白(/t)、換行(/n)等等
  - boundary_max_scan：設定文字距離，預設值為20
  - boundary_scanner：設定文字的樣式，支援chars、sentence、word
    - chars
    - sentence
    - word
  - boundary_scanner_locale：設定語言
  - encoder：設定編碼
  - fields：設定關鍵字做標記
  - force_source：設定標記文字做額外儲存
  - fragmenter：設定標記文字做段落
    - simple：將文字分成相同的大小
    - span：將文字分成相同的大小，但每段文字會以名詞優先
  - fragment_offset：調整標記文字顏色的邊界
  - fragment_size：設定標記文字的字符大小，預設100
  - highlight_query：高亮查詢
  - matched_fields：組合分段文字，取得文字名詞
  - no_match_size：設定匹配文字數量
  - number_of_fragments
  - order：設定文字排序
  - phrase_limit
  - pre_tags
  - post_tags
  - require_field_match
  - max_analyzed_offset
  - tags_schema：設定標籤模式
  - type

- 常用的範例





### 5.用laravel 如何Query Elasticsearch 取得Data

#### 5.1 安裝套件

```bash
composer require elasticsearch/elasticsearch
composer require tamayo/laravel-scout-elastic
composer require laravel/scout 
```

安裝好之後，在`config/app.php`新增這一行

```
'providers' =  [ 
    Laravel\Scout\ScoutServiceProvider::class, 
]
```

在執行這個指令產生`config/scout.php`檔案

```bash
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
```

修改`config/scout.php`檔案內容，`Index`自己取，例如我設定`elastic`

```php
'driver' =  env('SCOUT_DRIVER', 'elasticsearch'),

  'elasticsearch' =  [
    'index' =  env('ELASTICSEARCH_INDEX', 'Index自己取'),
    'hosts' =  [
      env('ELASTICSEARCH_HOST', 'http://localhost:9200'),
    ],
  ],
```

在`config/app.php`的`aliases`，新增`excel`的類別，可以方便做匯入

```bash
'aliases' => [
	'Excel' => Maatwebsite\Excel\Facades\Excel::class,
	...
]
```

#### 5.2 建立Commend檔案

執行下面指令產生`ESOpenCommend`

```bash
php artisan make:command ESOpenCommand
```

建立好之後，修改`handle()`的內容

```php
public function handle()
{
    //從config/scout.php有個hosts的值
    $host = config('scout.elasticsearch.hosts');
    
    //從config/scout.php有個index的值
    $index = config('scout.elasticsearch.index');
    
    //建立ElasticService物件
    $elasticService = new ElasticService();
    
    //產生ElasticSearch物件
    $client = $elasticService->connElastic();

    //如果有找到相同的索引值，會執行刪除
    if ($client->indices()->exists(['index' => $index])) {
        $client->indices()->delete(['index' => $index]);
    }

    //建立索引值資料
    return $client->indices()->create([
        'index' => $index, //索引值名稱
        'body' => [
            'settings' => [
                'number_of_shards' => 1,
                'number_of_replicas' => 0
            ],
            'mappings' => [
                    '_source' => [
                        'enabled' => true
                    ],
                    'properties' => [
                        'title' => [
                            'type' => 'text',
                            'analyzer' => 'ik_max_word',
                            'search_analyzer' => 'ik_smart'
                        ],
                        'author' => [
                            'type' => 'text',
                            'analyzer' => 'ik_max_word',
                            'search_analyzer' => 'ik_smart'
                        ],
                        'content' => [
                            'type' => 'text',
                            'analyzer' => 'ik_max_word',
                            'search_analyzer' => 'ik_smart'
                        ],
                        'create_date' => [
                            'type' => 'date'
                        ],
                    ],
                ]
            ]
        ]);
    }
```

我們要套過這個檔案告訴`elasticsearch`，建立索引值時有哪些欄位，做分詞設定

#### 5.3 建立model檔案

建立model

```bash
php artisan make:model Articles
```

建立好之後，修改內容

```php
class Articles extends Model
{
    use Searchable;
    protected  $table = 'articles';

    protected $dates = ['cearteDate'];

    protected $fillable = [
        'title',
        'author',
        'create_date',
        'content',
    ];

    //設定索引值
    public function searchableAs()
    {
        return 'elastic';
    }

    public function toSearchableArray()
    {
        $data = [
            'id' => $this->id,
            'title' => $this->title,
            'author' => $this->author,
            'content' => $this->content,
            'createDate' => $this->create_date,
        ];

        return $data;
    }

}

```

利用laravel Scout，將Articles做文章搜尋

#### 5.4 建立匯入的程式

```php
class ArticlesController extends Controller
{
	public function searchArticles(Request $request)
    {
        //取得關鍵字資料
        $keyword = $request->keyword;
        
        //執行模糊查詢
        $articles = $this->elasticService->fuzzySearch($keyword);
        
        //將查詢到的資料與關鍵字，丟到articles頁面上
        return view('articles', ['articles' => $articles, 'keyword' => $keyword]);
    }
    
    public function importArticles()
    {
        //用laravel excel套件，建立ArticlesImport類別，去找storage資料夾裡的articles.xls檔做匯入
        Excel::import(new ArticlesImport, storage_path('articles.xls'));
		
        //執行完之後，導頁到查詢畫面
        return redirect('/showArticles');
    }
}
```

```php
class ElasticService
{
    //建立ElasticSearch物件
    public function connElastic()
    {
        $elastic = Config::get('elastic');

        $client = ClientBuilder::create()->setHosts($elastic['hosts'])->build();

        return $client;
    }

   //帶關鍵字做模糊查詢
    public function fuzzySearch($search)
    {
        $params = [
            //從config/scout.php有個elasticsearch.index取得索引值
            'index' => config('scout.elasticsearch.index'),
            'body' => [
                //用createDate欄位做排序，由大至小
                'sort' => [
                    'createDate' => [
                        "order" => "desc"
                    ]
                ]
            ]
        ];

        //判斷關鍵字不等於空值和null
        if($search != '' && $search != null){
			
            //建立模糊查詢條件
            $params['body'] = [
                //設定模糊查詢，針對title、author、content，這3個欄位找資料
                'query' => [
                    'multi_match' => [
                        'query' => $search,
                        'fuzziness' => 'AUTO',
                        'fields' => ['title', 'author', 'content'],
                    ],
                ]
            ];
        }
        
        //建立ElasticSearch物件
        $client = $this->connElastic();
        
        //執行查詢
        $response = $client->search($params);
        
        //取得ElasticSearch的Index中，有符合模糊查詢條件的資料
        $data = $response['hits']['hits'];
        
        return $data;
    }
}
```

#### 5.5.新增索引值

**在產生索引值之前，請先在自己的DB，建立幾筆資料，我們會把DB資料連同索引值，一起新增到elasticsearch**

執行這個指令，產生elasticsearch的資料，用laravel scout

```bash
php artisan scout:import "App\Articles"
```

![elasticArticle7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle7.png>)



#### 5.6 CRUD

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
  - 語法為 `PUT _index/_doc/[ID\]`，例如：**PUT /users/_doc/1**
- `Update`(**PUT**)：
  - PUT 其實也可以作為更新 document 用，但更新的範圍是整個 document
  - 實際上，Elasticsearch document 是無法修改的；而更新這個操作其實是新增一個新的 document，將原有的 **_version** 加 1 後，舊的 document 被標示為 **deletion**
- `Partially Update`(**POST**)：
  - document 必須已經存在，更新時只會對 document 中相對應的欄位作增量更新 or 對應欄位的修改
  - json payload 需要包含在 `doc` 欄位中 (可參考[官網文件](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html))
  - 語法為 `POST _index/_update/[ID\]`，例如：**POST /users/_update/1**
  - POST 也可以拿來作為新增 document 用

**查詢資料**

```json
//查詢my-index-000001全部的資料
GET my-index-000001/_search
{

}
```

更新資料

```json
//更新my-index-000001，將tags欄位設定資料型態為keyword
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

刪除資料

```json
//刪除my-index-000001索引值
DELETE /my-index-000001
```

查詢資料

```
//查詢索引值my-index-000001的資料
POST /my-index-000001/_doc/1
{
  "doc":{"yeat": 2018}
}
```



參考資料:

- https://kknews.cc/zh-tw/code/qljaxbb.html


- https://www.google.com.tw/url?sa=i&url=https%3A%2F%2Fwww.awoo.com.tw%2Fblog%2Fgoogle-analytics%2F&psig=AOvVaw12rVYYZ7uRBFN3uDo7SZrK&ust=1596390215197000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCLia7v3I-uoCFQAAAAAdAAAAABAD

- https://github.com/cviebrock/laravel-elasticsearch

- https://www.elastic.co/guide/cn/elasticsearch/php/current/_configuration.html

- https://dotblogs.com.tw/supershowwei/2016/05/17/142101

- https://kknews.cc/zh-tw/code/pb3rvpp.html
- https://ithelp.ithome.com.tw/articles/10241212

- https://www.itread01.com/content/1564456805.html

- https://www.itread01.com/content/1549391948.html

- https://blog.tienyulin.com/elasticsearch-kibana-command-dsl-docker-compose/

- https://www.runoob.com/mysql/mysql-data-types.html

- https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html
- https://tw511.com/a/01/30925.html
- https://iter01.com/636347.html