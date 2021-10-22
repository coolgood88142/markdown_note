## Angular

### 什麼是Angular?

Angular是一個基於TypeScript的開發平台，通常被稱為 Angular 2+或 Angular v2及以上版本

### Angular和AngularJS的區別

Angular之前叫AngularJS ，後來升2之後，才正式將其正名為 **Angular** ，以便可以跟 1.x 版的 AngularJS 區分開來。

- Angular沒有 Scope或控制器的概念，相反，它使用組件的層次結構作為其主要的架構特徵。
- Angular有不同的表達式語法，重點是”[]”用於屬性綁定，”() “用於事件綁定
- 模組化 – 許多核心功能已轉移到模組上
- Angular推薦使用微軟的TypeScript語言，它引入了以下特性。

### 優點

- i18n - 多國語系
- Animation - 動畫
- Router - 路由
- CLI - 命令列工具（超強大！）
- Material - Google 力推的 Material 網頁設計框架
- Angular DevTools - 官方出版的 Google Chrome 開發者工具

### 範例

```html
<!DOCTYPE html>
<html>
  <head>  
    <meta charset="utf-8">  
    <meta name="viewport" content="width=device-width">  
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.0/angular.min.js"></script>  
    <title>JS Bin</title>
  </head>
  <body ng-app>
    <p>請輸入您的名字：</p>  
    <p><input id="name" ng-model="name" type="text"></p>  
    <h1>{{ "Hello " + name }}</h1>  
  </body>
</html>
```

輸入文字框時，會自動顯示Hello + 文字內容，例如：輸入John，會顯示Hello John。

參考資料

https://ithelp.ithome.com.tw/articles/10202827

https://developer.mozilla.org/zh-TW/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Angular_getting_started