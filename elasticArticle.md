---
title: "Elasticsearch實做匯出文章"
date: "2021-10-25"
author: "Elastic"
summary: "說明如何使用Elasticsearch實做匯出文章"
categories: ["Elastic"]
tags: ["Elastic","import","Article"]
---

## Elasticsearch實做匯出文章

以下介紹，用Elasticsearch實做匯出文章

## 安裝篇

### 1.下載Elasticsearch

https://www.elastic.co/downloads/elasticsearch

下載完後並解壓縮

### 2.啟動Elasticsearch

在bin的資料夾裡，執行elasticsearch.bat，看到127.0.0.1:9200代表啟動成功，在瀏覽器輸入http://127.0.0.1:9200/，就會看到Elasticsearch的資訊

![elasticArticle1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle1.png>)

![elasticArticle2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle2.png>)

### 3.下載Logstash

https://www.elastic.co/cn/downloads/logstash

下載完後並解壓縮

### 4.新增配置檔案

Logstash需要一個檔案，指定使用檔案中的事件做事情，新增logstash.conf檔案，並且放在bin資料夾裡

以下範例：logstash-simple.conf

```json
input { stdin { } }
output {
  elasticsearch { hosts => ["localhost:9200"] }
  stdout { codec => rubydebug }
}
```

新增檔案後，在bin的目錄底下執行指令，成功之後，在瀏覽器輸入http://127.0.0.1:9600/，就會看到Logsearch的資訊

```bash
logstash -f logstash-simple.conf
```

![elasticArticle3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle3.png>)

![elasticArticle4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle4.png>)

### 5.下載Kibana

https://www.elastic.co/cn/downloads/kibana

下載完後並解壓縮

### 6.啟動Kibana

在bin的資料夾裡，執行kibana.bat，看到127.0.0.1:5601代表啟動成功，在瀏覽器輸入http://127.0.0.1:5601/，就會看到kibana web界面，代表已經安裝成功。

![elasticArticle5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle5.png>)

![elasticArticle6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle7.png>)



參考資料:

https://linyencheng.github.io/2020/09/10/elastic-kibana-quick-start/、

https://kknews.cc/zh-tw/code/qljaxbb.html、

https://github.com/cviebrock/laravel-elasticsearch
