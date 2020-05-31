---
title: "Google Sheet"
date: "2020-05-29"
author: "Google Sheet"
summary: "安裝Google Sheet"
---

## Google Sheet

google sheet api是一個與google試算表溝通，能快速取得試算表資料，並且可進行編輯。

使用google sheet api需要啟用與建立憑證，以下介紹使用laravel 建立google sheet api，取得試算表資料。



#### 1.登入[Google API Console](https://console.developers.google.com/?hl=zh-tw)

登入google帳號之後，先新增專案，在搜尋google sheet api並且點選啟用。

![google_sheet1](https://coolgood88142.github.io/images/google_sheet1.png)



![google_sheet2](https://coolgood88142.github.io/images/google_sheet2.png)



#### 2.建立 OAuth 同意畫面

點選設定OAuth 同意畫面，User Type請選擇外部後點選建立，輸入應用程式名稱再儲存。

![google_sheet3](https://coolgood88142.github.io/images/google_sheet3.png)

![google_sheet4](https://coolgood88142.github.io/images/google_sheet4.png)



#### 3.建立 OAuth 用戶端ID

在建立憑證點選OAuth 用戶端ID，應用程式類型選擇網頁應用程式，並輸入名稱後建立，完成後會跳出用戶端ID與密碼，等等我們範例中會用到。

![google_sheet5](https://coolgood88142.github.io/images/google_sheet5.png)



![google_sheet6](https://coolgood88142.github.io/images/google_sheet6.png)

![google_sheet7](https://coolgood88142.github.io/images/google_sheet7.png)



#### 4.建立服務帳戶

點選管理服務帳戶，在建立服務帳戶，服務帳戶權限選擇角色管理員，`將這個服務帳戶的存取權限授予使用者`，這項可以不輸入點選完成。

![google_sheet8](https://coolgood88142.github.io/images/google_sheet8.png)

![google_sheet9](https://coolgood88142.github.io/images/google_sheet9.png)



![google_sheet10](https://coolgood88142.github.io/images/google_sheet10.png)



#### 5.建立服務帳戶金鑰

點選動作的建立金鑰，金鑰類型選擇JSON，系統會下載json檔案，在檔案名稱請自行更換，例如:credentials.json。

![google_sheet11](https://coolgood88142.github.io/images/google_sheet11.png)

![google_sheet12](https://coolgood88142.github.io/images/google_sheet12.png)

![google_sheet13](https://coolgood88142.github.io/images/google_sheet13.png)



#### 6.試算表建立json url資料

建立之前試算表要設定好權限共用，打開指令碼編輯器，在程式碼.gs新增doGet function，在執行部署為網路應用程式後，就會建立json url

![google_sheet14](https://coolgood88142.github.io/images/google_sheet14.png)

程式碼.gs

```javascript
function doGet(e) {
  //建立試算表的url與工作表名稱
  let SpreadSheet = SpreadsheetApp.openById('1YiYQkvgspoaE9E-RBwwNYOnTz_2Tgtuo4RpApDbvrVQ')
  
  //設定試算表的工作表名稱
  let SheetName = SpreadSheet.getSheetByName('user');
  
  //取得工作表的最後一欄與列
  let lastColumn = SheetName.getLastColumn();
  let lastRow = SheetName.getLastRow();
  
  //取得資料範圍，從第2欄第1列開始
  let values = SpreadSheet.getSheetValues(2, 1, lastRow-1, lastColumn);  
    
  return ContentService
  .createTextOutput(JSON.stringify(values))
  .setMimeType(ContentService.MimeType.JSON); 
}
```

![google_sheet15](https://coolgood88142.github.io/images/google_sheet15.png)

![google_sheet16](https://coolgood88142.github.io/images/google_sheet16.png)

![google_sheet17](https://coolgood88142.github.io/images/google_sheet17.png)



#### 6.安裝laravel-google-sheets套件

在laravel專案安裝套件

```php
composer require revolution/laravel-google-sheets
```



#### 7.laravel專案設定api

建立laravel專案後，在.env檔案建立連線，GOOGLE_CLIENT_ID、GOOGLE_CLIENT_SECRET是OAuth 用戶端ID產生的ID與密碼，GOOGLE_SERVICE_ACCOUNT_JSON_LOCATION是找storage目錄底下的服務帳戶金鑰的json檔。

```php
GOOGLE_APPLICATION_NAME=
GOOGLE_CLIENT_ID=22356814133-7mtrn8f457ua7cthoanbkgq0u5ir72m1.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=Hw1FYu-BQZ6UuZwTWV6PY4zg
GOOGLE_REDIRECT=
GOOGLE_DEVELOPER_KEY=
GOOGLE_SERVICE_ENABLED=true
GOOGLE_SERVICE_ACCOUNT_JSON_LOCATION=../storage/credentials.json
```



#### 8.在Controller新增程式碼

在web設定route執行Controller的function

```php
Route::get('/sheet','FormController@getSheetData'));
```

程式碼

```php
public function getSheetData(Request $request)
{
    $json = json_decode(file_get_contents('https://script.googleusercontent.com/macros/echo?user_content_key=G0gFjpiurUPLaSHvFZ2YchbwiaPgJ60OcaKdsq-H3CskyjkCGrU4TLERE3W0u15PMQraweo4Qo9luQadxKUWskIk908STA2vm5_BxDlH2jW0nuo2oDemN9CCS2h10ox_1xSncGQajx_ryfhECjZEnG5NHNdb1AwE-EYeNMc_zIN8h0XP-arDU-ftinAuwVa9i3l2C0b2upz5QyC6G6Hbeg&lib=Me3WC3MqZMXdn2WOzQ1Jhz481CpkraLGD'), true);
        $data = [];
        foreach($json as $key => $value){
            $array = [];
            foreach($value as $a => $b){
                array_push($array, $b);
            }
            array_push($data, $array);
        }

        dd($data);
}
```





參考資料:

https://github.com/kawax/laravel-google-sheets、

https://drivemarketing.ca/en/blog/connecting-laravel-to-a-google-sheet/、

https://www.oxxostudio.tw/articles/201706/google-spreadsheet-1.html



