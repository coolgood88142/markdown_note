---
title: "vue pagination"
date: "2020-01-09"
author: "pagination"
summary: "介紹使用vue分頁"
---

以下介紹vue分頁的做法，在laravel上使用vue、axios來建立表格分頁



### 架構

我們可以laravel利用axios執行reute，取得controller的response資料，在組好資料後顯示頁面上

![1](C:\xampp\htdocs\markdown_note\assets\images\pagination.png)

#### UserController.php

```php
<?php

namespace App\Http\Controllers;

use App\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
use Carbon\Carbon;
use Config;

class UserController extends Controller
{
    public function userView(){
        return view('user');
    }

    public function index()
    {
        $users = DB::table('user')->paginate(5);
        $response = [
            'pagination' => [
                'total' => $users->total(),
                'per_page' => $users->perPage(),
                'current_page' => $users->currentPage(),
                'last_page' => $users->lastPage(),
                'from' => $users->firstItem(),
                'to' => $users->lastItem()
            ],
            'users' => $users
        ];
        //確認user資料是否為重複，要補上文件

        return response()->json($response);
    }

    public function getCityData()
    {
        $city = Config::get('city');
        $counties = array();
        $districts = array();
        $i=0;$j=0;

        foreach($city as $key => $value){
            $counties[$i]['text'] = $city[$key]['counties'];
            $counties[$i]['value'] = ($i+1);
            $k=0;
            $city_districts = array();
            foreach($city[$key]['districts'] as $district){
                $value = $city[$key]['value'];
                $city_districts[$j]['text'] = $district;
                $city_districts[$j]['value'] = $value[$k];
                $k++;
                $j++;
            }
            $districts[$i] = $city_districts;
            $i++;
        }


        return view('form', ['counties' => $counties, 'districts' => $districts]);
    }
```



#### web.php

```php
Route::get('/user','userController@userView');
Route::get('/getUserData','userController@index');
```



#### user.js

```javascript
import 'bootstrap/dist/css/bootstrap.css'

let user = new Vue({
    el: '#app',
    data: {
        users: [],
        pagination: {
            total: 0,
            per_page: 2,
            from: 1,
            to: 0,
            current_page: 1
        },
        offset: 4
    },
    computed: {
        isActived: function () {
            return this.pagination.current_page;
        },
        pagesNumber: function () {
            if (!this.pagination.to) {
                return [];
            }
            let from = this.pagination.current_page - this.offset;
            if (from < 1) {
                from = 1;
            }
            let to = from + (this.offset * 2);
            if (to >= this.pagination.last_page) {
                to = this.pagination.last_page;
            }
            let pagesArray = [];
            while (from <= to) {
                pagesArray.push(from);
                from++;
            }
            return pagesArray;
        }
    },
    mounted: function () {
        this.getUserData(this.pagination.current_page)
    },
    methods: {
        getUserData: function (page) {
            axios.get('/getUserData?page=' + page).then(response => {
                this.users = response.data.users.data,
                this.pagination = response.data.pagination
            }).catch((error) => {
                //顯示請求資料失敗的錯誤訊息
                if (error.response){
                    //在log顯示response錯誤的資料、狀態、表頭
                    console.log(error.response.data);
                    console.log(error.response.status);
                    console.log(error.response.headers);
                }else{
                    //在log顯示r錯誤訊息
                    console.log('Error',error.message);
                }
                
            })
        },
        changePage: function (page) {
            this.pagination.current_page = page
            this.getUserData(page)
        }
    }
})
```



#### user.blade.php

```html
<html>

<head>
    <meta name="csrf-token" content="{{ csrf_token() }}">
</head>
<body>
    <div class="container">
        <div v-cloak id="app" class="content">
            <h2 id="title" class="text-center text-black font-weight-bold" style="margin-bottom:20px;">使用者資料</h2>
            <table id="example" class="table table-striped table-bordered" style="width:100%">
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>姓名</th>
                        <th>信箱</th>
                        <th>狀態</th>
                        <th>編輯設定</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="user in users">
                        <td>@{{ user.us_id }}</td>
                        <td>@{{ user.us_name }}</td>
                        <td>@{{ user.us_email }}</td>
                        <td>@{{ user.us_status == 1 ? '正常' : '停用'  }}</td>
                        <td><input type="button" class="btn btn-primary" value="編輯" v-on:click="editUserData(user.us_id)" /></td>
                    </tr>
                </tbody>
            </table>

            <nav>
                <ul class="pagination">
                    <li class="page-item" v-if="pagination.current_page > 1">
                        <a class="page-link" href="#" aria-label="Previous" v-on:click="changePage(pagination.current_page - 1)">
                            <span aria-hidden="true">&laquo;</span>
                        </a>
                    </li>
                    <li class="page-item" v-for="page in pagesNumber" :class="[ page == isActived ? 'active' : '']">
                        <a class="page-link" href="#" v-on:click="changePage(page)">@{{ page }}</a>
                    </li>
                    <li class="page-item" v-if="pagination.current_page < pagination.last_page">
                        <a class="page-link" href="#" aria-label="Next" v-on:click="changePage(pagination.current_page + 1)">
                            <span aria-hidden="true">&raquo;</span>
                        </a>
                    </li>
                </ul>
            </nav>
        </div>
    </div>
    <script src="{{mix('js/app.js')}}"></script>
    <script src="{{mix('js/user.js')}}"></script>
</body>

</html>
```

說明整個程式流程，怎麼執行的



參考資料:

https://www.itsolutionstuff.com/post/laravel-5-and-vue-js-crud-with-pagination-example-and-demo-from-scratchexample.html、

https://www.kancloud.cn/yunye/axios/234845、





