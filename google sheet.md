---
title: "Google Sheet"
date: "2020-05-29"
author: "Google Sheet"
summary: "安裝Google Sheet"
---

## Google Sheet

google sheet api是一個與google試算表溝通，能快速取得試算表資料，並且可進行編輯。

使用google sheet api需要啟用與建立憑證，以下介紹使用laravel 建立google sheet api，取得試算表資料。

![google_sheet0](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet0.png>)



#### 1.登入[Google API Console](https://console.developers.google.com/?hl=zh-tw)

登入google帳號之後，先新增專案，在搜尋google sheet api並且點選啟用。

![google_sheet1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet1.png>)



![google_sheet2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet2.png>)



#### 2.建立OAuth 同意畫面

點選設定OAuth 同意畫面，User Type請選擇外部後點選建立，輸入應用程式名稱再儲存。

![google_sheet3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet3.png>)

![google_sheet4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet4.png>)

User Type的內部，必須是G Suite 方案的google帳號才可以使用 ，G Suite是google專為企業設計的雲端辦公套組，提供多種google工具，提高工作效率。

G Suite 方案的google帳號需要付費，這裡建議選擇外部即可。



#### 3.建立 OAuth 用戶端ID

在建立憑證點選OAuth 用戶端ID，應用程式類型選擇網頁應用程式，並輸入名稱後建立，完成後會跳出用戶端ID與密碼，等等我們範例中會用到。

![google_sheet5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet5.png>)



![google_sheet6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet6.png>)

![google_sheet7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet7.png>)



#### 4.建立服務帳戶

點選管理服務帳戶，在建立服務帳戶，服務帳戶權限選擇角色管理員，`將這個服務帳戶的存取權限授予使用者`，這項可以不輸入點選完成。

![google_sheet8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet8.png>)

![google_sheet9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet9.png>)



![google_sheet10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet10.png>)



#### 5.建立服務帳戶金鑰

點選動作的建立金鑰，金鑰類型選擇JSON，系統會下載json檔案，在檔案名稱請自行更換，例如:credentials.json。

![google_sheet11](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet11.png>)

![google_sheet12](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet12.png>)

![google_sheet13](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet13.png>)

OAuth 用戶端ID主要是同意授權使用API資料，服務帳戶則是利用金鑰json檔取得API權限。

要優先OAuth 授權哪種應用程式使用API，以上範例為網頁應用程式。

當專案使用OAuth 用戶端時，會用access token(權杖)授權使用API，在用服務帳戶才有權限使用google API拿到試算表資料。

P12為PKCS 12，是一個有時效性的私密金鑰檔案(.p12)，由建立憑證取得API資訊。



#### 6.安裝laravel-google-sheets套件

在laravel專案安裝套件

```php
composer require revolution/laravel-google-sheets
```

執行完後再產生config檔，這時會在config資料夾產生google.php

```php
php artisan vendor:publish --provider="PulkitJalan\Google\GoogleServiceProvider" --tag="config"
```

google.php

```php
'application_name' => env('GOOGLE_APPLICATION_NAME', ''),
'client_id'        => env('GOOGLE_CLIENT_ID', ''),
'client_secret'    => env('GOOGLE_CLIENT_SECRET', ''),
'redirect_uri'     => env('GOOGLE_REDIRECT', ''),
'scopes'           => '',
'access_type'      => 'offline',
'approval_prompt'  => 'auto',
'developer_key' => env('GOOGLE_DEVELOPER_KEY', ''),
'service' => [
        'enable' => env('GOOGLE_SERVICE_ENABLED', false),
        'file' => env('GOOGLE_SERVICE_ACCOUNT_JSON_LOCATION',''),
    ],

```



#### 7.laravel專案設定api

建立laravel專案後，在.env檔案建立連線，GOOGLE_CLIENT_ID、GOOGLE_CLIENT_SECRET是OAuth 用戶端ID產生的ID與密碼，GOOGLE_SERVICE_ACCOUNT_JSON_LOCATION是找storage目錄底下的服務帳戶金鑰的json檔。config的google.php檔也要設定一樣，主要是設定access token

.env檔

```php
GOOGLE_APPLICATION_NAME=
GOOGLE_CLIENT_ID=22356814133-7mtrn8f457ua7cthoanbkgq0u5ir72m1.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=Hw1FYu-BQZ6UuZwTWV6PY4zg
GOOGLE_REDIRECT=
GOOGLE_DEVELOPER_KEY=
GOOGLE_SERVICE_ENABLED=true
GOOGLE_SERVICE_ACCOUNT_JSON_LOCATION=../storage/credentials.json
```

google.php

```php
'application_name' => env('GOOGLE_APPLICATION_NAME', ''),
'client_id'        => env('GOOGLE_CLIENT_ID', '703199608133-48lihk64psruguqdqk1qqr508i87jt3v.apps.googleusercontent.com'),
'client_secret'    => env('GOOGLE_CLIENT_SECRET', 'Hw1FYu-BQZ6UuZwTWV6PY4zg'),
'redirect_uri'     => env('GOOGLE_REDIRECT', ''),
'scopes'           => [\Google_Service_Sheets::DRIVE, \Google_Service_Sheets::SPREADSHEETS],,
'access_type'      => 'offline',
'approval_prompt'  => 'auto',
'developer_key' => env('GOOGLE_DEVELOPER_KEY', ''),
'service' => [
        'enable' => env('GOOGLE_SERVICE_ENABLED', true),
        'file' => env('GOOGLE_SERVICE_ACCOUNT_JSON_LOCATION',storage_path('credetokenntials.json')),
    ],
```



#### 8.在Controller新增程式碼

在web設定route執行Controller的function，執行`php artisan serve`,

web.php

```php
Route::get('/sheet','FormController@getSheetData'));
```

程式碼

```php
public function getSheetData(Request $request)
{
   $sheets = Sheets::spreadsheet('1YiYQkvgspoaE9E-RBwwNYOnTz_2Tgtuo4RpApDbvrVQ')->sheet('user');
   $sheetsValue = $sheets->all();
   //顯示試算表所有資料
   //dd($sheetsValue);
    
    //更新資料
    $sheets->range('A4')->update([['3', 'Hello', 'hello@yahoo.com.tw']]);
	$values = Sheets::range('')->all();
    // [
    //   ['ID', '姓名', 'email'],
    //   ['1', 'Terry', 'terry@gmail.com'],
    //   ['2', 'Ming', 'i3@yahoo.com.tw'],
    //   ['3', 'Hello', 'hello@yahoo.com.tw']
    //]
    
    //新增資料
    $sheets->append([['4', 'Eric', 'eric@gmail.com']]);
    // [
    //   ['ID', '姓名', 'email'],
    //   ['1', 'Terry', 'terry@gmail.com'],
    //   ['2', 'Ming', 'i3@yahoo.com.tw'],
    //   ['3', 'Hello', 'hello@yahoo.com.tw']
    //   ['4', 'Eric', 'eric@gmail.com']
    //]
    
    //產生新的工作表
    Sheets::spreadsheetByTitle('1YiYQkvgspoaE9E-RBwwNYOnTz_2Tgtuo4RpApDbvrVQ'))->addSheet('SheetTitle');
    
    //移除工作表
    Sheets::spreadsheetByTitle('1YiYQkvgspoaE9E-RBwwNYOnTz_2Tgtuo4RpApDbvrVQ'))->deleteSheet('SheetTitle');
}
```

![google_sheet18](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/google_sheet18.png>)





#### google app script與google sheet api比較差異

google app script是google開發出來的腳本，是用於javascript操作，而laravel的google sheet api是在laravel專案啟用



#### 總結

laravel使用laravel-google-sheets套件，建立google sheet api的設定，先設定google 的OAuth 用戶端，才有權限使用google 服務，之後再設定服務帳戶，建立金鑰json檔。

在專案上的env檔與google.php來建立api資訊，才可以取得試算表資料。



參考資料:

https://github.com/kawax/laravel-google-sheets、

https://drivemarketing.ca/en/blog/connecting-laravel-to-a-google-sheet/、

https://www.oxxostudio.tw/articles/201706/google-spreadsheet-1.html、

https://gsuite.google.com.tw/intl/zh-TW/features/、

http://vito-note.blogspot.com/2015/04/aspnet-google-oauth2.html、

[https://xenby.com/b/245-%E6%95%99%E5%AD%B8-google-oauth-2-0-%E7%94%B3%E8%AB%8B%E8%88%87%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97](https://xenby.com/b/245-教學-google-oauth-2-0-申請與使用指南)、

http://vito-note.blogspot.com/2015/04/google-oauth-20.html、

https://support.google.com/admanager/answer/6078734?hl=zh-Hant、

https://developers.google.com/identity/protocols/oauth2/web-server

