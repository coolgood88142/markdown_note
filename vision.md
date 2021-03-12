---
title: "圖片解析"
date: "2021-03-12"
author: "Google Cloud Vision API"
summary: "使用Google服務做圖片解析"
categories: ["API document"]
tags: ["圖片解析",Vision","Google Cloud Vision API","Translate","Google Cloud Translate API","Storage","Google Cloud Storage API"]
---

## 圖片解析

### 大綱

介紹

建立憑證

1. 登入Google API Console
2. 建立OAuth 同意畫面
3. 建立 OAuth 用戶端ID
4. 建立服務帳戶
5. 建立服務帳戶金鑰

安裝套件

laravel專案設定api

新增js檔

新增compoent

- keyword.vue
- upload.vue
- edit.vue
- imagel.vue
- editButton.vue
- deleteButton.vue

新增頁面

新增Controller

Route

編譯檔案

啟動本機站台

### 介紹

我們需要用到google cloud vision API、google cloud translate API、google cloud storage API，3種API

vision API主要取得解析圖片的物件，拿物件中的圖片解析的關鍵字當資料

translate API是把關鍵字的資料中，有英文的資料做英翻中

storage API是將圖片檔案存放到Google Cloud Storage裡，在讀取路徑方便我們在網頁上呈現。

### 建立憑證

使用google api需要啟用與建立憑證，以下介紹步驟

#### 1.登入[Google API Console](https://console.developers.google.com/?hl=zh-tw)

登入google帳號之後，先新增專案，在搜尋google vision api、google cloud translate API、 cloud storage，並且點選啟用。

![vision-1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-1.png>)

![vision-2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-2.png>)

![vision-3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-3.png>)

![vision-4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-4.png>)

#### 2.建立OAuth 同意畫面

點選設定OAuth 同意畫面，User Type請選擇外部後點選建立，輸入應用程式名稱再儲存。

![vision-5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-5.png>)

![vision-6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-6.png>)

這裡選擇外部之後，在輸入email即可。

#### 3.建立 OAuth 用戶端ID

在建立憑證點選OAuth 用戶端ID，應用程式類型選擇網頁應用程式，並輸入名稱後建立，完成後會跳出用戶端ID與密碼，等等我們範例中會用到。

![vision-7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-7.png>)



![vision-8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-8.png>)

![vision-9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-9.png>)

#### 4.建立服務帳戶

點選管理服務帳戶，在建立服務帳戶，服務帳戶權限選擇角色管理員

![vision-10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-10.png>)

![vision-11](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-11.png>)

#### 5.建立服務帳戶金鑰

在服務帳戶裡點選管理帳戶，在建立新的金鑰，金鑰類型選擇JSON，系統會下載json檔案，在檔案名稱請自行更換，例如:credentials.json。

![vision-12](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-12.png>)

![vision-13](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-13.png>)

![vision-14](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vision-14.png>)

OAuth 用戶端ID主要是同意授權使用API資料，服務帳戶則是利用金鑰json檔取得API權限。

要優先OAuth 授權哪種應用程式使用API，以上範例為網頁應用程式。

當專案使用OAuth 用戶端時，會用access token(權杖)授權使用API，在用服務帳戶才有權限使用google API。

### 安裝套件

安裝vision、translate、storage、laravel vue datatable

```php
composer require google/cloud-vision
composer require google/cloud-translate
composer require google/cloud-storage
npm install laravel-vue-datatable
```

### laravel專案設定api

建立laravel專案後，在.env檔案建立連線，GOOGLE_CLOUD_PROJECT是在google cloud建立的專案名稱、GOOGLE_APPLICATION_CREDENTIALS是找storage目錄底下的服務帳戶金鑰的json檔，檔案是從。

.env檔

```php
GOOGLE_CLOUD_PROJECT=專案名稱
GOOGLE_APPLICATION_CREDENTIALS=../storage/credentials.json
```

### 新增js檔

在resources/js路徑底下，新增vision.js檔案，這裡引入兩個component。

```javascript
import keyword from "./components/vision/keyword.vue"
import upload from "./components/vision/upload.vue"

let user = new Vue({
    el: "#app",
    data: {
        url: './getVision',
        saveVisionUrl: './upload',
        showModal: false,
    },
    components: {
        'keyword': keyword,
        'upload': upload,
    },
    
})
```

### 新增component

#### keyword.vue

```vue
<template>
    <div>
        <data-table 
            :data="data"
            :columns="columns">
        </data-table>
        <edit
            :english-keywords="englishKeywords" :chinese-keywords="chineseKeywords" :key-word-id="id">
        </edit>
    </div>
</template>

<script>
import imageCell from './imageCell';
import edit from './edit';
import editButton from './editButton';
import deleteButton from './deleteButton';
import DataTable from 'laravel-vue-datatable';
Vue.use(DataTable);

export default {
    props: {
		keyWordUrl: {
			type: String,
		},
    },
    components: {
        imageCell,
        edit,
        editButton,
        deleteButton,
    },
    data() {
        return {
            url:  this.keyWordUrl,
            data: {},
            tableProps: {
                search: '',
                length: 50,
                column: 'no',
                dir: 'asc'
            },
            columns: [
                {
                    label: '圖片',
                    name: 'image',
                    orderable: true,
                    component: imageCell,
                },
                {
                    label: '英文關鍵字',
                    name: 'englishKeyword',
                    orderable: true,
                    width: 30,
                },
                {
                    label: '中文關鍵字',
                    name: 'chineseKeyword',
                    orderable: true,
                    width: 30,
                },
                {
                    label: '操作',
                    name: '',
                    orderable: false,
                    width: 10,
                    component: editButton,
                    event: "click",
                    handler: this.updateSelectedModal,
                }, 
                {
                    label: '刪除',
                    name: '',
                    orderable: false,
                    classes: { 
                        'btn': true,
                        'btn-primary': true,
                        'btn-sm': true,
                    },
                    width: 10,
                    component: deleteButton,
                    event: "click",
                    handler: this.deleteKeyWord,
                }, 
            ],
            englishKeywords: [],
            chineseKeywords: [],
            id: null,
        }
    },
    created() {
        this.getData(this.url);
    },
    methods:{
        getData(url = this.url, options = this.tableProps) {
            axios.get(url, {
                params: options
            })
            .then(response => {
                this.data = response.data;
            })
            .catch(errors => {
            })
        },
        updateSelectedModal(data) {
            this.englishKeywords = data['englishKeyword'].split(","); 
            this.chineseKeywords = data['chineseKeyword'].split(","); 
            this.id = data["editId"];
        },
        deleteKeyWord(data){
            const params = {
				id: data["editId"],
			}
            axios.post('/deleteKeyWord', params).then((response) => {
				if (response.data.status === "success") {
					swal({
						title: response.data.message,
						confirmButtonColor: "#e6b930",
						icon: response.data.status,
						showCloseButton: true,
					}).then(() => {
						window.location.href = '/vision'
					});
				}
			}).catch((error) => {
				if (error.response) {
					console.log(error.response.data)
					console.log(error.response.status)
					console.log(error.response.headers)
				} else {
					console.log("Error", error.message)
				}
			})
        }
    },
    watch:{
        alarmUrl(val){
            this.url = val
            this.getData(this.url);
        }
    }
}
</script>
```

這裡引用laravel-vue-datatable套件，以及圖片、按鈕、編輯功能的component物件，updateSelectedModal是設定中文關鍵字與ID資料，getData與deleteKeyWord，是用axios傳到後端做查詢或刪除資料。

#### upload.vue

```vue
<template>
    <form method="POST" :action="uploadUrl" enctype="multipart/form-data" class="form-inline">
        <div class="form-group">
        <label for="po_image" class="block text-sm leading-5 font-medium text-gray-700">
            圖片上傳
            
        </label>
        <input type="file" class="form-control-file" name="po_image">
        </div>
        <div class="form-group">
            <div id="query" class="col-min-btn col-md-3 col-lg-2" style="text-align:right;">
                <input type="submit" name="query_data" class="btn btn-primary" value="上傳" >
            </div>
        </div>
        
    </form>
</template>

<script>
export default {
     props: {
		uploadUrl: {
			type: String,
		},
    },
}
</script>
```

從vision.js拿到上傳檔案的url，上傳檔案後執行submit

#### edit.vue

```vue
<template>
    <div class="modal" id="exampleModal" tabindex="-1" role="dialog" aria-hidden="true">
        <div class="modal-dialog modal-lg" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">keywords</h5>
                    <div id="query" class="col-min-btn col-md-3 col-lg-2" style="text-align:right;">
                        <input type="button" name="query_data" class="btn btn-primary" value="新增" v-on:click="addKeyWord()">
                    </div>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                    
                </div>
                <div class="modal-body">
                    <div class="row">
                        <div class="col">英文關鍵字</div>
                        <div class="col">中文關鍵字</div>
                        <div class="w-100"></div>
                        <div class="col-6">
                            <table id="entable" class="table" v-sortable="{ onUpdateEnglish: onUpdateEnglish }">
                               <!-- 英文關鍵字表格內容 -->
                            </table>
                        </div>
                        <div class="col-6">
                            <table id="chtable" class="table" v-sortable="{ onUpdateChinese: onUpdateChinese }">
                                <!-- 中文關鍵字表格內容 -->
                            </table>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <!-- <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button> -->
                    <div class="form-check form-check-inline">
						<input type="button" class="btn btn-secondary" id="cancel" name="cancel"
								value="取消" data-dismiss="modal">
					</div>
					<div class="form-check form-check-inline">
						<input type="button" class="btn btn-primary" id="save" name="save"
								value="儲存" @click="saveKeyword()">
					</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

Vue.directive('sortable', {
  inserted: function (el, binding) {
    new Sortable(el, binding.value || {})
  }
})

export default {
    props: {
        englishKeywords: {
            type: Array,
        },
        chineseKeywords: {
            type: Array,
        },
        keyWordId: {
            type: Number,
        }
    },
    data() {
        return {
            'keywords': [
                this.englishKeywords,
                this.chineseKeywords,
            ],
            'enKeywords': this.englishKeywords,
            'chKeywords': this.chineseKeywords,
            'id' : this.keyWordId
        }
    },
    methods:{
        onUpdateEnglish(event) {
            let now = this.enKeywords[event.newIndex]
            let old = this.enKeywords[event.oldIndex]

            this.enKeywords[event.newIndex] = old
            this.enKeywords[event.oldIndex] = now
        },
        onUpdateChinese(event) {
            let now = this.chKeywords[event.newIndex]
            let old = this.chKeywords[event.oldIndex]

            this.chKeywords[event.newIndex] = old
            this.chKeywords[event.oldIndex] = now
        },
        addKeyWord(){
            let length1 = this.enKeywords.length
            let length2 = this.chKeywords.length

            this.enKeywords.splice(length1+1, 0, '')
            this.chKeywords.splice(length2+1, 0, '')
        },
        deleteEnglishKeyWord(index){
            this.enKeywords.splice(index, 1)
        },
        deleteChineseKeyWord(index){
            this.chKeywords.splice(index, 1)
        },
        upEnglishKeyWord(index){
            let now = this.enKeywords[index];
            let before = this.enKeywords[index-1];
            if(now != undefined && before != undefined){
                this.enKeywords.splice(index-1, 0, now)
                this.enKeywords.splice(index+1, 1)
            }
        },
        upChineseKeyWord(index){
            let now = this.chKeywords[index];
            let before = this.chKeywords[index-1];
            if(now != undefined && before != undefined){
                this.chKeywords.splice(index-1, 0, now)
                this.chKeywords.splice(index+1, 1)
            }
        },
        downEnglishKeyWord(index){
            let now = this.enKeywords[index];
            let after = this.enKeywords[index+1];
            if(now != undefined && after != undefined){
                this.enKeywords.splice(index, 0, after)
                this.enKeywords.splice(index+2, 1)
            }
        },
        downChineseKeyWord(index){
            let now = this.chKeywords[index];
            let after = this.chKeywords[index+1];
            if(now != undefined && after != undefined){
                this.chKeywords.splice(index, 0, after)
                this.chKeywords.splice(index+2, 1)
            }
        },
        saveKeyword(){
            const params = {
				id: this.id,
                english_keyword: this.getEnglishKeyWord(),
                chinese_keyword: this.getChineseKeyWord()
			}
            axios.post('/saveKeyWord', params).then((response) => {
				if (response.data.status === "success") {
					swal({
						title: response.data.message,
						confirmButtonColor: "#e6b930",
						icon: response.data.status,
						showCloseButton: true,
					}).then(() => {
						window.location.href = '/vision'
					});
				}
			}).catch((error) => {
				if (error.response) {
					console.log(error.response.data)
					console.log(error.response.status)
					console.log(error.response.headers)
				} else {
					console.log("Error", error.message)
				}
			})

        },
        getEnglishKeyWord(){
            let keyword = '';
            let ketElement = document.getElementsByName('enkeys[]');
            ketElement.forEach((e, index) => {
                if(e.value == ''){
                    swal({
                        title: '英文關鍵字第' + index + '筆資料不能為空',
                        confirmButtonColor: "#e6b930",
                        icon: 'error',
                        showCloseButton: true,
                    })
                    keyword = ''
                }else{
                    keyword = keyword + e.value + ','
                }
            })

            if(keyword != ''){
               keyword = keyword.substring(0,keyword.length-1)
            }

            return keyword
        },
        getChineseKeyWord(){
            let keyword = '';
            let ketElement = document.getElementsByName('chkeys[]');
            ketElement.forEach((e, index) => {
                if(e.value == ''){
                    swal({
                        title: '中文關鍵字第' + index + '筆資料不能為空',
                        confirmButtonColor: "#e6b930",
                        icon: 'error',
                        showCloseButton: true,
                    })
                    keyword = ''
                }else{
                    keyword = keyword + e.value + ','
                }
            })

            if(keyword != ''){
               keyword = keyword.substring(0,keyword.length-1)
            }

            return keyword
        },
        changeEnglishKeyWord(index){
            let ketElement = document.getElementsByName('enkeys[]');
            this.enKeywords[index] = ketElement[index].value
        },
        changeChineseKeyWord(index){
            let ketElement = document.getElementsByName('chkeys[]');
            this.chKeywords[index] = ketElement[index].value
        }
    },
    watch:{
        keyWordId(val){
            this.id = val
            return val
        },
        englishKeywords(val){
            this.enKeywords = val
            return val
        },
        chineseKeywords(val){
            this.chKeywords = val
            return val
        },
    }
}
</script>
```

顯示編輯畫面，並且可以將中英文關鍵字做編輯，可以做排序、滑鼠拖曳、新增、刪除、儲存，當中英文關鍵字改好之後做儲存，這時會跑到後端更新DB資料，網頁會在重新整理。

onUpdateEnglish與onUpdateChinese是中英文關鍵字做排序時會更換資料，deleteEnglishKeyWord與deleteChineseKeyWord是移除中英文關鍵字的資料，saveKeyword是儲存中英文關鍵字的資料，changeEnglishKeyWord與changeChineseKeyWord是用滑鼠拖曳會將中英文關鍵字做排序。

#### image.vue

```vue
<template>
    <img :src="data.image" />
</template>

<script>
export default {
    props: {
        data: {
            type: Object,
        }
    },
}
</script>
```

將圖片路徑放在component裡

#### editButton.vue

```vue
<template>
    <button
        type="button"
        data-toggle="modal"
        @click="click(data)"
        class="btn btn-primary"
        data-target="#exampleModal">
        編輯
    </button>
</template>
<script>
export default {
    props: {
        data: {},
        name: {},
        click: {},
        meta: {},
        classes: {},
    },
}
</script>
```

設定編輯按鈕使用bootstrap的modal，要對應顯示編輯視窗

#### deleteButton.vue

```vue
<template>
    <button
        type="button"
        @click="click(data)"
        class="btn btn-danger">
        刪除
    </button>
</template>
<script>
export default {
    props: {
        data: {},
        name: {},
        click: {},
        meta: {},
        classes: {},
    },
}
</script>
```

設定刪除按鈕，移除該筆圖片的關鍵字資料與圖片檔案

### 新增頁面

```html
<html>
    <head>
        <meta name="csrf-token" content="{{ csrf_token() }}">
        <link rel="stylesheet" href="/css/vision.css">
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.4.0/font/bootstrap-icons.css">
        <!-- SortableJS -->
  <script src="https://unpkg.com/sortablejs@1.4.2"></script>
  <!-- VueSortable -->
  <script src="https://unpkg.com/vue-sortable@0.1.3"></script>
      </head>
    <body>
        <div id="app" class="container">
            <form id="fugle_form" name="fugle_form" action="" method="POST" class="sidebar-form" style="margin-top:70px;">    
                <h2 id="title" class="text-center text-black font-weight-bold" style="margin-bottom:20px;">圖片解析</h2>
                <upload :upload-url="saveVisionUrl"></upload>
                <keyword :key-word-url="url"></keyword>
            </form>
        </div>
    </body>
    <script src="{{mix('js/app.js')}}"></script>
    <script src="{{mix('js/vision.js')}}"></script>
</html>
```

### 新增Controller

在專案的目錄下，輸入指令新增`CloudVisionController.php`

```jsx
php artisan make:controller CloudVisionController
```

```php
public function getVisionDataTable(Request $request)
    {
        $db = DB::table('vision');
        $length = $request->input('length');
        $sortBy = $request->input('column');
        $orderBy = $request->input('dir');
        $searchValue = $request->input('search');
        $dataArray = [];
        $visionData = $db->get()->toArray();
        foreach($visionData as $data){
            $array = [];
            $array['image'] = $data->image;
            $array['englishKeyword'] = $data->english_keyword;
            $array['chineseKeyword'] = $data->chinese_keyword;
            $array['editId'] = $data->id;
            array_push($dataArray, (object)$array);
        }

        $collection = collect($dataArray);

        if($searchValue != ''){
            $collection->search(function ($item, $key) {
                return $item->$key == $searchValue;
            });
        }
        
        if($sortBy != '' && $orderBy != ''){
            if($orderBy == 'asc'){
                $collection = $collection->sortBy($sortBy);
            }else if($orderBy == 'desc'){
                $collection = $collection->sortByDesc($sortBy);
            }
        }

        if($length != ''){
            $collection = $collection->forPage(1, $length);
        }

        return new DataTableCollectionResource($collection->values()->all());
    }

    public function saveCloudVision(Request $request){
        //先做圖片調整大小後暫存
        $poImage = $request->po_image;
        // $a = $poImage->size;
        $size = (int)(($poImage->getSize()) / 1024);
        $imageName = $poImage->getClientOriginalName();
        if($size > 1024){
            $message = $imageName . ' 檔案大小不可超過1M ';
            return $message;
        }
        $file_name = uniqid().'.'.$poImage->getClientOriginalExtension();
        $file_path = public_path('images');
        if (!is_dir($file_path)){
            mkdir($file_path);
        }
        $thumbnail_file_path = $file_path . '\\' .$file_name;
        $image = Image::make($poImage)->resize(300, null, function ($constraint) {$constraint->aspectRatio();})->save($thumbnail_file_path, 60);
        //再從本機的storage的圖片上傳
        $storage = new StorageClient([
            'projectId' => 'useVision'
        ]);
        $uploadFile = fopen($thumbnail_file_path , 'r');
        $bucketName = 'vision-save-image';
        $bucket = $storage->bucket($bucketName);
        
        $bucket->upload($uploadFile, [
            'name' => $imageName
        ]);

        $imageAnnotator = new ImageAnnotatorClient();
        $imageContext = new ImageContext();
        $imageContext->setLanguageHints(['zh-Hant']);
        $dataArray = [];
        $englishKeyword = '';
        $chineseKeyword = '';
        $googleStroageIamge = 'https://storage.googleapis.com/' . $bucket->name() . '/' .$imageName;
        try {
            $file = @file_get_contents($googleStroageIamge);
            $labelDetection = $imageAnnotator->labelDetection($file, [
                'imageContext' => $imageContext
            ]);
            $labelData = $labelDetection->getLabelAnnotations();
            if(count($labelData) > 0){
                foreach($labelData as $data){
                    //組標籤資料的關鍵字
                }
                if($englishKeyword != ''){
                    $translate =  $this->getTranslateClient($englishKeyword, 'zh-Hant');
                    $englishKeyword = substr($englishKeyword,0,-1);
                    $chineseKeyword = str_replace('，', ',', $translate['text']);
                }
            }

            $textDetection = $imageAnnotator->textDetection($file, [
                'imageContext' => $imageContext
            ]);
            $textData = $textDetection->getTextAnnotations();
            if(count($textData) > 0){
                //組文字資料的關鍵字
            }
            
            if($englishKeyword != '' || $chineseKeyword != ''){
                $chineseKeyword = substr($chineseKeyword,0,-1);
                $dbData = [
                    'image' => (String)$googleStroageIamge,
                    'english_keyword' => (String)$englishKeyword,
                    'chinese_keyword' => (String)$chineseKeyword, 
                ];
                array_push($dataArray, $dbData);
            } else {
                $message = $imageName . '此圖並無特徵';
                return $message;
            }

            if(count($dataArray) > 0){
                $db = DB::table('vision')->insert($dataArray);
            }

        } catch (Exception $e) {
            $message = '新增失敗!';
            return $message;
        }

        $imageAnnotator->close();
        if(file_exists($thumbnail_file_path)){
            unlink($thumbnail_file_path);
        }

        $key = '344';
		$time = time();
       	$token = [
        	'iss' => 'http://www.helloweba.net',
           	'aud' => 'http://www.helloweba.net', 
           	'iat' => $time,
           	'nbf' => $time , 
           	'exp' => $time+7200,
            'data' => [ 
             	'page' => 'vision',
            ]
        ];

        return view('vision', [
            'token' => '/?token=' . (JWT::encode($token, $key)),
        ]);
    }

    public function saveKeyWordData(Request $request){
        $english_keyword = $request->english_keyword;
        $chinese_keyword = $request->chinese_keyword;
        $status = 'success';
        $message = '更新成功!';

        $data = [ 
            'english_keyword' => $english_keyword,
            'chinese_keyword' => $chinese_keyword,
        ];

        try {
            $user = DB::table('vision')->where('id', $request->id)->update($data);

        } catch (Exception $e) {
            $status = 'error';
            $message = '更新失敗!';
            dd($e);
        }
         return [ 'status' => $status, 'message' => $message, ];

    }

    public function deleteKeyWordData(Request $request){
        $id = $request->id;
        $status = 'success';
        $message = '刪除成功!';

        try {
            $db = DB::table('vision')->where('id', $id);
            $data = $db->get();
            $path = $data[0]->image;
            $fileObj = explode('/', $path);
            $file = $fileObj[4];

            $storage = new StorageClient([
                'projectId' => 'useVision'
            ]);
    
            $bucketName = 'vision-save-image';
            $bucket = $storage->bucket($bucketName);
            $object = $bucket->object($file);
            $object->delete();

            $db->delete();

        } catch (Exception $e) {
            $status = 'error';
            $message = '刪除失敗!';
            dd($e);
        }
         return [ 'status' => $status, 'message' => $message, ];

    }

    public function getTranslateClient(String $text, String $targetLanguage){
        $translate = new TranslateClient();
        $result = $translate->translate($text, [
            'target' => $targetLanguage,
        ]);

        return $result;
    }

```

程式碼中主要是做CURD，在網頁剛開啟時，透過axios做查詢，將資料顯示到網頁上

新增是在上傳圖片的時候做，會先將用storage API將圖片做調整大小，在上傳上去google cloud storage，再用vision API取得解析圖片的物件，這裡我們使用標籤與文字，標籤是依照API識別圖片中人事物，文字則是取得圖片文字，如果圖片沒有文字就沒資料，當標籤或文字，如果是英文的話，會用translate API做英翻中，並且將資料新增到中文關鍵字裡。

更新是在編輯視窗裡，改好關鍵字資料在做儲存，刪除則是直接移除一筆圖片資料與檔案，移除圖片檔案有使用translate API，將google cloud storage圖片檔案做移除。

### Route

在web設定route執行Controller的function，執行`php artisan serve`,

web.php

```php
Route::get('/vision', 'CloudVisionController@getVisionDefaultData')->name('vision');

Route::post('/upload', 'CloudVisionController@saveCloudVision')->name('upload');

Route::post('/saveKeyWord', 'CloudVisionController@saveKeyWordData')->name('saveKeyWord');

Route::post('/deleteKeyWord', 'CloudVisionController@deleteKeyWordData')->name('deleteKeyWord');
```

### 編譯檔案

上面步驟完成之後，我們要進行編譯，請在`webpack.mix.js`檔案，新增`vision.js`檔案

```jsx
mix.js('resources/js/app.js', 'public/js')
   .js('resources/js/vision.js', 'public/js')
    .sass('resources/sass/app.scss', 'public/css');
```

做完之後，執行編譯指令

```jsx
npm run watch
```

### 啟動本機站台

```jsx
$ php artisan serve
Starting Laravel development server: <http://127.0.0.1:8000>
```

啟動之後，輸入網址為http://127.0.0.1:8000/vision

參考資料:

[https://xenby.com/b/245-%E6%95%99%E5%AD%B8-google-oauth-2-0-%E7%94%B3%E8%AB%8B%E8%88%87%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97](https://xenby.com/b/245-教學-google-oauth-2-0-申請與使用指南)、

http://vito-note.blogspot.com/2015/04/google-oauth-20.html、

https://support.google.com/admanager/answer/6078734?hl=zh-Hant、

https://developers.google.com/identity/protocols/oauth2/web-server、

https://github.com/googleapis/google-cloud-php-vision、

http://googleapis.github.io/google-cloud-php/#/docs/cloud-vision/v1.3.1/vision/visionclient、

https://cloud.google.com/translate/docs/reference/libraries/v3/php、

https://cloud.google.com/storage/docs/reference/libraries、

https://github.com/jamesdordoy/laravel-vue-datatable、

https://jamesdordoy.github.io/laravel-vue-datatable



