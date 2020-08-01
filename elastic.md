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

#### Logsearch

主要是收集日誌的主機上，server端負責將收到的各節點日誌進行過濾、修改等操作在一併發往elasticsearch上去。

#### Kibana

是做為Elasticsearch、Logsearch的Web界面，匯整數據資料。



以下介紹，在Windows安裝ELK

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

Logstash需要一個檔案，指定使用檔案中的事件做事情，新增logstach.conf檔案，並且放在bin資料夾裡，檔案中file的path是取log檔案，也要新增相同的檔名，內容空白沒關西

以下範例：logstach.conf

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

在檔案的目錄底下執行指令

```javascript
logstash -f logstash.conf
```



![elastic3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic3.png>)



參考資料:

https://kknews.cc/zh-tw/code/qljaxbb.html、

https://www.google.com.tw/url?sa=i&url=https%3A%2F%2Fwww.awoo.com.tw%2Fblog%2Fgoogle-analytics%2F&psig=AOvVaw12rVYYZ7uRBFN3uDo7SZrK&ust=1596390215197000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCLia7v3I-uoCFQAAAAAdAAAAABAD、