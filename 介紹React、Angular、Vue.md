### 介紹React、Angular、Vue

### 前言

前端開發技術React、Vue、Angular目前這三個框架很熱門，React 主要使用Javascript ES6，而Vue 則是 ES5、ES6皆可，比較特別的是Angular使用的是Typescript，Typescript是基於Javascript的擴充。

### React

React（也被稱為React.js或ReactJS）是一個用於構建用戶介面的JavaScript庫。它由Facebook和一個由個人開發者和公司組成的社區來維護。

React可以作為開發單頁或移動應用的基礎。然而，React只關注向DOM渲染數據，因此創建React應用通常需要使用額外的庫來進行狀態管理和路由，Redux和React Router分別是這類庫的例子。

```html
<div id="myReactApp"></div>
<script type="text/babel">
	function Greeter(props){
		return <h1>{props.greeting}</h1>
	}
	var App = <Greeter greeting="Hello World!" />;
	ReactDOM.render(App.dociment.getElementById("myReactApp"));
```

Greeter函數是一個React組件，它接受一個屬性問候語。變數App是Greeter組件的一個實例，其中問候語屬性被設置為 “Hello World！”。然後，ReactDOM.render方法將我們的Greeter組件渲染在DOM元素（id為 myReactApp）中。

### Angular

Angular（通常被稱為 “Angular 2+”或 “Angular v2及以上版本”）是一個基於TypeScript的開源Web應用框架，由Google的Angular團隊和由個人以及企業組成的社區領導。 Angular是由構建AngularJS的同一個團隊從零開始重寫的。

### Angular和AngularJS的區別

- Angular沒有 “Scope”或控制器的概念，相反，它使用組件的層次結構作為其主要的架構特徵。
- Angular有不同的表達式語法，重點是”[]”用於屬性綁定，”() “用於事件綁定
- 模組化 – 許多核心功能已轉移到模組上
- Angular推薦使用微軟的TypeScript語言，它引入了以下特性。

### Vue

Vue.js（通常被稱為Vue；發音為/vjuː/，類似於 “view”）是一個開源的Model-view-viewmodel JavaScript框架，用於構建用戶介面和單頁面應用程式。它由Evan You創建，由他和來自Netlify和Netguru等多家公司的核心成員維護。

Vue.js的特點是，它採用了一個漸進式的架構，專註於聲明式渲染和組件合成。複雜應用所需的高級功能，如路由、狀態管理和構建工具等，都是通過官方維護的支援庫和包提供的，其中Nuxt.js是最受歡迎的解決方案之一。

Vue.js可以讓你用稱為指令（directives）的HTML屬性來擴展HTML。



### 參考資料

https://codingnote.cc/zh-tw/p/122128/

https://medium.com/@publiclou123/vue-react-angular%E6%AF%94%E8%BC%83-bceb38c1b458
