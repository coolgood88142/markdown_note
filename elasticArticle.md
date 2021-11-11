---
title: "Elasticsearch實做匯出文章"
date: "2021-10-25"
author: "Elastic"
summary: "說明如何使用Elasticsearch實做匯出文章"
categories: ["Elastic"]
tags: ["Elastic","import","Article"]
---

## Elasticsearch實做文章模糊查詢

以下介紹，用Elasticsearch實做文章模糊查詢

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

![elasticArticle6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle6.png>)

## laravel 專案開發

### 1.流程圖

![elasticArticle8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle8.png>)

![elasticArticle10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle10.png>)

```markdown
title 新增資料

actor user
participant user
participant 新增文章
participant addArticles

user->新增文章:建立文章物件
activate user
activate 新增文章
新增文章->addArticles:執行addArticles Route
activate addArticles
新增文章<--addArticles:回傳articles資料
deactivate addArticles
user<--新增文章:回傳文章資料
deactivate 新增文章
deactivate user
```

![elasticArticle9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle9.png>)

![elasticArticle11](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle11.png>)

```markdown
title 新增索引值

actor user
participant user
participant 新增索引值
participant 輸入指令新增索引值

user->新增索引值:建立索引值物件
activate user
activate 新增索引值
activate 輸入指令新增索引值
新增索引值->輸入指令新增索引值:輸入指令新增
新增索引值<--輸入指令新增索引值:回傳索引值資料
user<--新增索引值:回傳索引值資料
deactivate 新增索引值
deactivate user
```

### 2.安裝套件

已經有laravel專案的情況下，安裝這三個套件

```bash
composer require elasticsearch/elasticsearch
composer require tamayo/laravel-scout-elastic
composer require laravel/scout 
```

安裝好之後，在`config/app.php`新增這兩行

```php
'providers' =  [ 
    Laravel\Scout\ScoutServiceProvider::class, 
    ScoutEngines\Elasticsearch\ElasticsearchProvider::class, 
]
```

在執行這個指令產生`config/scout.php`檔案

```php
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
```

修改`config/scout.php`檔案內容，Index自己取，例如我設定elastic

```php
'driver' =  env('SCOUT_DRIVER', 'elasticsearch'),

  'elasticsearch' =  [
    'index' =  env('ELASTICSEARCH_INDEX', 'Index自己取'),
    'hosts' =  [
      env('ELASTICSEARCH_HOST', 'http://localhost:9200'),
    ],
  ],
```

### 3.建立Commend檔案

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

### 4.建立Model檔案

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
        'content',
    ];

    /**
     * Set the content attribute.
     *
     * @param $value
     */
    public function setContentAttribute($value)
    {
        $data = [
            'raw'  => $value,
            'html' => (new Markdowner)->convertMarkdownToHtml($value)
        ];

        $this->attributes['content'] = json_encode($data);
    }

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

### 5.新增索引值

**在產生索引值之前，請先在自己的DB，先建立幾筆資料，我們會把DB資料連同索引值，一起新增到elasticsearch**

執行這個指令，產生elasticsearch

```bash
php artisan scout:import "App\Articles"
```

![elasticArticle7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elasticArticle7.png>)

### 7.程式撰寫

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
        //從config/scout.php有個elasticsearch.index取得索引值
        $params = ['index' => config('scout.elasticsearch.index')];

        //判斷關鍵字不等於空值和null
        if($search != '' && $search != null){
			
            //建立模糊查詢條件
            $params['body'] = [
                //用createDate欄位做排序，由大至小
                'sort' => [
                    'createDate' => [
                        "order" => "desc"
                    ]
                ],
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



### 參考資料:

https://linyencheng.github.io/2020/09/10/elastic-kibana-quick-start/、

https://kknews.cc/zh-tw/code/qljaxbb.html、

https://github.com/cviebrock/laravel-elasticsearch

https://github.com/babenkoivan/scout-elasticsearch-driver

https://zhuanlan.zhihu.com/p/215756862

https://learnku.com/articles/40289
