---
title: "vue"
date: "2020-01-09"
author: "vue"
summary: "介紹vue幾個用法"
---





## emit

vue的component想將資料傳給instance時，要使用emit，自定義事件來觸發，將emit是件整的包出去，在頁面上component的標籤裡，監聽了emit的事件，當觸發時監聽到emit的事件去對應要執行function

```javascript
$emit('事件名稱',傳遞參數) //參數可帶或不帶
```



以下範例，component名稱為addmessage，emit事件為add-text又帶text參數，頁面上的addmessage監聽add-text事件，要執行addText function。

```php+HTML
<div id="app">
    {{ message }}
    <br/><br/>
    <addmessage v-on:add-text="addText"></addmessage>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js"></script>
```



```javascript
Vue.component('addmessage', {
    data:function(){
      return{
        text:'world'
      }
    },
    template: '<button id="test" v-on:click="$emit(\'add-text\',text)">新增world</button>'
})


let app = new Vue({
    el: '#app',
    data:{
        message: 'Hello'
    },
    methods:{
      addText(text){
        this.message += text;
      }
    }
})
```

[emit連結](https://codepen.io/coolgood88142/pen/LYErYgQ)



## props

instance要將資料傳到vue的component，要使用props，必須先設定傳進來的值是什麼資料型態，接收到props的資料就會做更新

```html
<component :addtext="message"></component>
```



以下範例，文字框中綁定了message屬性，資料帶`Hello Vue!`，component名稱為addname，建立props名稱為addtext資料型態為字串，賦予message屬性，這時文字框的文字更新時，addtext的資料就會跟著更新。

```php+HTML
<div id="app">
    <input v-model="message">
    <br/><br/>
    <addname :addtext="message"></addname>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js"></script>
```



```javascript
Vue.component('addname', {
    props: {
      addtext: String
    },
    template: '<p>Props文字：{{ addtext }}</p>'
})

let app = new Vue({
    el: '#app',
    data:{
        message: 'Hello Vue!'
    }
})
```

[props連結](https://codepen.io/coolgood88142/pen/qBEKYjy)



## computed

computed為計算屬性，分為get與set(讀取與設值)，如果沒寫預設是get，在component或是instance，可以在內部資料做計算，資料改變時computed就會執行計算，做完後回傳資料

這裡需要清楚什麼時候要使用get與set的時間點，







參考資料:

https://dotblogs.com.tw/wasichris/2017/03/04/021726、

https://chenuin.blogspot.com/2019/01/vuejs-propsemit.html、

https://blog.csdn.net/lander_xiong/article/details/79018737、

https://pjchender.blogspot.com/2017/05/vue-computed-getter-setter.html



