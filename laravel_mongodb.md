---
title: "laravel_mongodb"
date: "2020-12-10"
author: "Mongodb"
summary: "說明使用laravel建立新專案，並且使用mongodb"
categories: ["Mongodb"]
tags: ["laravel","Mongodb"]
---

### laravel_mongodb

#### 1.建立新專案

建立專案名稱為laravel_fblog

```
composer create-project --prefer-dist laravel/laravel laravel_fblog 6.*
```

#### 2.安裝mongodb套件

```jsx
composer require jenssegers/mongodb ^3.6
```

安裝成功之後，在laravel的config/app.php的providers，新增這行

```
Jenssegers\\Mongodb\\MongodbServiceProvider::class,
```

#### 3.設定mongodb連線

mongodb與mysql的設定，兩種都會設定在`.env`、`database.php`

```php
//.env
//兩種使用的連線參數都一樣，差在DB_PORT，mysql是3306，mongodb是27017
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=users
DB_USERNAME=root
DB_PASSWORD=

DB_CONNECTION=mongodb
DB_HOST=127.0.0.1
DB_PORT=27017
DB_DATABASE=myfblog
DB_USERNAME=
DB_PASSWORD=
```

```php
//database
'connections' => [
	'mysql' => [
		'driver' => 'mysql',
        'url' => env('DATABASE_URL'),
        'host' => env('DB_HOST', '127.0.0.1'),
        'port' => env('DB_PORT', '3306'),
        'database' => env('DB_DATABASE', 'users'),
        'username' => env('DB_USERNAME', 'root'),
        'password' => env('DB_PASSWORD', ''),
        'unix_socket' => env('DB_SOCKET', ''),
        'charset' => 'utf8mb4',
        'collation' => 'utf8mb4_unicode_ci',
        'prefix' => '',
        'prefix_indexes' => true,
        'strict' => true,
        'engine' => null,
        'options' => extension_loaded('pdo_mysql') ? array_filter([
               PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
         ]) : [],
	],

  'mongodb' => [
		'driver' => 'mongodb',
		'host' => env('DB_HOST', '127.0.0.1'),
        'port' => env('DB_PORT', 27017),
        'database' => env('DB_DATABASE', 'myfblog'),
        'username' => env('DB_USERNAME', ''),
        'password' => env('DB_PASSWORD', ''),
        'options' => [
                'database' => env('DB_AUTHENTICATION_DATABASE', 'admin'),
        ],
  ],
]
```

#### 4.新增Models

```
php artisan make:model Models/Articles
```

修改Articles.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Articles extends Model
{
    //mysql要設定table name
    //protected $table = 'users';

    //mongodb要設定collection
    protected $collection= 'articles';
    public $timestamps = false;
}
```

如果兩種連線方式都想要在專案上使用，在新增model的時候，設定好用`table name`還是`collection`。

#### 5.建立migration

建立articles資料表

```
php artisan make:migration create_articles_table
```

#### 6.建立factories

```php
php artisan make:factory ArticlesFactory
```

修改ArticlesFactory

```php
<?php

/** @var \Illuminate\Database\Eloquent\Factory $factory */

use App\Models\Articles;
use Faker\Generator as Faker;

$factory->define(Articles::class, function (Faker $faker) {
    return [
        'title' => $faker->title(),
        'content' => $faker->text(),
        'slug' => $faker->slug(),
    ];
});

```

#### 7.建立seeder

```
php artisan make:seeder ArticlesTableSeeder
```

修改ArticlesTableSeeder

```php
<?php

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class ArticlesTableSeeder extends Seeder
{
    /**
     *  Adding dummy articles
     */
    public function run()
    {
        factory(Articles::class, 5)->create();
    }
}

```

在執行seeder建立資料

```
php artisan db:seed --class=ArticlesTableSeeder
```

#### 8.新增CURD

##### 建立Controller

```
php artisan make:controller ArticlesController
```

```php
<?php

namespace App\Http\Controllers;

use App\Models\Articles;
use Illuminate\Http\Request;
use Carbon\Carbon;

class ArticlesController extends Controller
{
    //讀取（Read）
    //laravel的做法是用all取得全部的資料
    public function getArticlesData()
    {
        $articles = Articles::all();
        $response = [
            'articles' => $articles,
            'add' => route('addArticles'),
            'update' => route('updateArticles'),
            'delete' => route('deleteArticles')
        ];

        return view('articles', ['data' => $response]);
    }

    public function checkArticlesData($id)
    {
        $isSuccess = false;
        $data = Articles::all()->contains($id);
        if ($data > 0) {
            $isSuccess = true;
        }

        return $isSuccess;
    }

    //新增（Create）
    //laravel的做法是用save儲存資料
    public function addArticlesData(Request $request)
    {
        $title = $request->title;
        $content = $request->content;
        $slug = $request->slug;
        $status = 'success';
        $message = '新增成功!';

        try {
            $articles = new Articles();
            $articles->title = $title;
            $articles->content = $content;
            $articles->slug = $slug;
            $articles->creation_date = Carbon::now();
            $articles->save();
        } catch (Exception $e) {
            echo $e;
        }
        
        return [ 
            'status' => $status,
            'message' => $message 
        ];
    }

    //更新（Update）
    //laravel的做法是用save更新資料
    public function updateArticlesData(Request $request)
    {
        $id = $request->id;
        $title = $request->title;
        $content = $request->content;
        $slug = $request->slug;
        $status = 'success';
        $message = '更新成功!';

        try {
            $check = $this->checkArticlesData($id);

            if ($check) {
                $articles = Articles::find($id);
                $articles->title = $title;
                $articles->content = $content;
                $articles->slug = $slug;
                $articles->save();
            } else {
                $status = 'error';
                $message = '更新失敗!';
            }

        } catch (Exception $e) {
            echo $e;
        }
        
        return [ 
            'status' => $status,
            'message' => $message 
        ];
    }

    //刪除（Delete）
    //laravel的做法是用delete刪除資料
    public function deleteArticlesData(Request $request)
    {
        $id = $request->id;
        $status = 'success';
        $message = '刪除成功!';

        try {
            $check = $this->checkArticlesData($id);

            if ($check) {
                $articles = Articles::find($id);
                $articles->delete();
            } else {
                $status = 'error';
                $message = '刪除失敗!';
            }

        } catch (Exception $e) {
            echo $e;
        }
        
        return [ 
            'status' => $status,
            'message' => $message 
        ];
    }

}

```

#### 9.設定Route

在routes/web.php，新增這幾行

```php
Route::get('/article', 'ArticlesController@getArticlesData')->name('getArticlesData');

Route::post('/addArticle', 'ArticlesController@addArticlesData')->name('addArticles');

Route::post('/updateArticle', 'ArticlesController@updateArticlesData')->name('updateArticles');

Route::post('/deleteArticle', 'ArticlesController@deleteArticlesData')->name('deleteArticles');
```

#### 10.新增blade頁面

建立articles.blade.php

```html
<html>
    <head>
        <meta name="csrf-token" content="{{ csrf_token() }}">
        <meta http-equiv="Access-Control-Allow-Origin" content="*" />
    </head>
    <body>
        <div class="container">
            <div v-cloak id="app" class="content">
                <form action="" method="POST">
                    {{ csrf_field() }}
                    <h2 
                        id="title" 
                        class="text-center text-black font-weight-bold" 
                        style="margin-bottom:20px;">
                    文章查詢
                    </h2>
                    <div style="text-align:right">
                        <input type="button" id="btn_insert" class="btn btn-primary" @click="getAddArticles()" value="新增" />
                    </div><br/>
                    <articles-list
                        :articles-data="articles" 
                        :url-delete="urlDelete"
                        @update-articles="updateArticles"
                        @delete-articles-data="deleteArticlesData"
                        @is-show-message="isShowMessage"
                    >
                    </articles-list>
                    <articles-edit 
                        v-if="showEdit" 
                        @close="showEdit = false" 
                        :edit-title="editTitle"
                        :is-add="isAdd"
                        :url-add="urlAdd"
                        :url-update="urlUpdate"
                        :params="params"
                        @update-articles-data="updateArticlesData"
                        @is-show-message="isShowMessage"
                        
                    >
                    </articles-edit>
                </form>
                <input type="hidden" id="data" value="{{ json_encode($data) }}">
            </div>
        </div>
        <script src="{{mix('js/app.js')}}"></script>
        <script src="{{mix('js/articles.js')}}"></script>
        <link rel="stylesheet" type="text/css" href={{mix('css/app.css')}}>
    </body>
</html>
```

#### 11.安裝Vue

```
npm install --save-dev vue@2.6.12
```

在專案中的resources/js/app.js檔案，新增變數Vue

```javascript
window.Vue = require('vue');
```

#### 12.新增js

在專案中的resources/js路徑中，建立articles.js

```javascript
import articlesList from './components/articles/List.vue';
import articlesEdit from './components/articles/Edit.vue';

let data = JSON.parse(document.getElementById('data').value);

let app = new Vue({
    el: '#app',
    components: {
        'articles-list': articlesList,
        'articles-edit': articlesEdit
    },
    data: {
        editTitle: '',
        showEdit: false,
        isAdd: false,
        articles: data.articles,
        urlAdd: data.add,
        urlUpdate: data.update,
        urlDelete: data.delete,
        params: {
            'id': '',
            'title': '',
            'content': '',
            'slug': ''
        },
        articlesDataIndex: null
    },
    methods: {
        getAddArticles() {
            this.editTitle = '新增文章'
            this.showEdit = true
            this.isAdd = true
            this.params = {
                'id': '',
                'title': '',
                'content': '',
                'slug': ''
            }
        },
        updateArticles(id, title, content, slug, index) {
            this.editTitle = '更新文章'
            this.showEdit = true
            this.params = {
                'id': id,
                'title': title,
                'content': content,
                'slug': slug
            }
            this.articlesDataIndex = index
            this.isAdd = false
        },
        updateArticlesData(params) {
            if (this.articlesDataIndex != null) {
                this.articles[this.articlesDataIndex].title = params.title
                this.articles[this.articlesDataIndex].content = params.content
                this.articles[this.articlesDataIndex].slug = params.slug
            }else{
                this.articles.push(params)
                this.articlesDataIndex = null
            }
        },
        deleteArticlesData(index) {
            this.articlesDataIndex = index
            if (this.articlesDataIndex != null) {
                this.articles.splice(this.articlesDataIndex, 1)
            }
        },
        isShowMessage(isSuccess, message){
            let isAdd =  this.isAdd
            swal({
                title: message,
                icon: isSuccess ? 'success':'error',
                buttons:  'OK',
                dangerMode: true,
            }).then(function() {
                if (isSuccess && isAdd){
                    location.reload()
                }
            })

            if (isSuccess) {
                this.showEdit = false
            }
        }
    },
})
```

#### 13.新增component

在專案中的resources/js/component/articles路徑中，新增兩個`component`檔案，`Edit.vue`、`List.vue`

```javascript
//Edit.vue
<template>
	<transition name="modal">
		<div class="modal-mask">
			<div class="modal-wrapper">
				<div class="modal-container">
					<form action="" method="POST" class="sidebar-form">
						<div class="modal-header">
							<slot name="header">
								<h3>{{ editTitle }}</h3>
							</slot>
						</div>
						<div class="modal-body">
							<slot name="body">
								<div class="form-group">
									<label for="inputTitle">標題</label>
									<input type="text" class="form-control" id="inputTitle" v-model="title">
								</div>
								<div class="form-group">
									<label for="inputContent">內容</label>
									<input type="text" class="form-control" id="inputContent" v-model="content">
								</div>
								<div class="form-group">
									<label for="inputSlug">網址 slug</label>
									<input type="text" class="form-control" id="inputSlug" v-model="slug">
								</div>
							</slot>
						</div>
						<div class="modal-footer">
							<slot name="footer">
								<div class="form-check form-check-inline">
									<input type="button" class="btn btn-primary" id="cancel" name="cancel"
											value="取消" @click="$emit('close')">
								</div>
								<div class="form-check form-check-inline">
									<input type="button" class="btn btn-primary" id="save" name="save"
											value="儲存" @click="saveArticles()">
									</div>
							</slot>
						</div>
					</form>
				</div>
			</div>
		</div>
	</transition>
</template>

<style>
	.modal-container{
		width:480px
	}
</style>

<script>
import swal from "sweetalert"

export default {
	props: {
		editTitle: {
			type:String
		},
		params: {
			type:Object
		},
		urlAdd: {
			type:String
		},
		urlUpdate: {
			type:String
		},
		isAdd: {
			type:Boolean
		}
	},
	data() {
		return {
			title: this.params.title,
			content: this.params.content,
			slug:this.params.slug,
			messageText: '',
		}
	},
	methods: {
		checkArticles(title, content, slug) {
			let isSuccess = false
			this.messageText = ''
			
			if (title === '' && content === '' && slug === '') {
				this.messageText = "標題或文章內容或網址不能為空"
			} else if (title === '') {
				this.messageText = "標題不能為空"
			} else if (content === '') {
				this.messageText = "文章內容不能為空"
			} else if (slug === '') {
				this.messageText = "網址不能為空"
			} 

			if (this.messageText == '') {
				isSuccess = true
			} else {
				this.$emit('is-show-message', isSuccess, this.messageText)
			}
			return isSuccess
		},
		saveArticles(){
			let title = this.title
			let content = this.content
            let slug = this.slug
            let url = this.isAdd ? this.urlAdd : this.urlUpdate
			let isSuccess = this.checkArticles(title, content, slug)

			if (isSuccess) {
				let data = {
					'title': title,
					'content': content,
					'slug': slug
				}

				if (!this.isAdd) {
					data['id'] = this.params.id
				}

				axios.post(url, data).then((response) => {
					let isSuccess = response.data.status == 'success' ? true : false
					if (isSuccess && !this.isAdd) {
						this.$emit('update-articles-data', data)
					}
					this.$emit('is-show-message', isSuccess, response.data.message)
				}).catch((error) => {
					if (error.response) {
						console.log(error.response.data);
						console.log(error.response.status);
						console.log(error.response.headers);
					} else {
						console.log('Error', error.message);
					}

					let ErrorMessage = '新增失敗!'
					if (!this.isAdd) {
						ErrorMessage = '更新失敗!'
					}
					this.$emit('is-show-message', false, ErrorMessage)
				})
			}
			
		}
	},
}
</script>
```

```javascript
//List.vue
<template>
	<div id="channel_list">
		<div class="form-group" id="select">
			<table class="table table-striped table-bordered" style="width:100%">
				<tr>
					<th>標題</th>
					<th>文章內容</th>
					<th>網址slug</th>
					<th style="width: 150px">編輯設定</th>
				</tr>
				<tr v-for="(articles, index) in articlesData" :key="index">
					<th>{{ articles.title }}</th>
					<th>{{ articles.content }}</th>
					<th>{{ articles.slug }}</th>
					<td>
						<input type="button" class="btn btn-primary" value="編輯" @click="$emit('update-articles', articles._id, articles.title, articles.content, articles.slug, index)" />
						<input type="button" class="btn btn-primary" value="刪除" @click="deleteArticles(articles._id, index)" />
					</td>
				</tr>
			</table>
		</div>
		</div>
</template>

<script>
export default {
	props:{
		articlesData: {
			type:Array
		},
		urlDelete: {
			type:String
		}
	},
	data() {
		return {
			btnSelect: 'btn btn-primary',
			seleted: '',
			showData: this.articlesData
		}
	},
	methods: {
		deleteArticles(id, index) {
			let url = this.urlDelete
			let params = {
                id: id
			}
			
			axios.post(url, params).then((response) => {
                let isSuccess = response.data.status == 'success' ? true : false
				if (isSuccess) {
					this.$emit('delete-articles-data', index)
				}
                this.$emit('is-show-message', isSuccess, response.data.message)
            }).catch((error) => {
                if (error.response) {
                    console.log(error.response.data);
                    console.log(error.response.status);
                    console.log(error.response.headers);
                } else {
                	console.log('Error', error.message);
                }
                this.$emit('is-show-message', false, '刪除失敗!')
            })
		}
	},
}
</script>
```

#### 14.設定webpack.mix.js

在webpack.mix.js檔案，新增要編譯的js檔案

```javascript
mix.js('resources/js/app.js', 'public/js')
   .js('resources/js/articles.js', 'public/js')
    .sass('resources/sass/app.scss', 'public/css');
```

執行編譯

```
npm run watch
```

#### 15.啟動本機站台

```
php artisan serve
```

