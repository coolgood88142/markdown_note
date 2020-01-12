---
title: "vue"
date: "2020-01-09"
author: "vue"
summary: "介紹vue幾個用法"
---



## emit

vue的component想將資料傳給instance時，要使用emit，必須是事件或監聽觸發。

以下範例為按鈕按下去時，message文字會新增World文字



補上emit用法與使用時機，用簡單的範例時做要可以跑起來，要放個連結(codepen)

```php+HTML
<div id="app">
    @{{ message }}
    <addmessage v-on:add-text="addText"></addmessage>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js"></script>
```



```javascript
Vue.component('addmessage', {
    template: '<button id="" v-on:click="$emit('add-text','World')"></button>'
})


let app = new Vue({
    el: '#app',
    data:{
        message: 'Hello'
    },
    components:{
        'addmessage': addmessage
    },
    methods: {
        addText(text) {
            this.message += text;
        }
    }
})
```





## props

instance要將資料傳到vue的component，要使用props，必須先設定傳進來的值是什麼資料型態，在template裡



頁面上有文字框，綁定屬性為name，在js裡預設值為空字串，這裡設定addname的componentes，內容是顯示一個參數裡放文字，





```php+HTML
<div id="app">
    <input type="text" v-model="name">
    <addname :inputName="name"></addname>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js"></script>
```



```javascript
Vue.component('addname', {
    template: '<span>{{inputName}}</span>',
    props: {
      inputName: String
    }
})


let app = new Vue({
    el: '#app',
    data:{
        name: ''
    },
    components:{
        'addname': addname
    }
})
```





參考資料:

https://dotblogs.com.tw/wasichris/2017/03/04/021726、

https://chenuin.blogspot.com/2019/01/vuejs-propsemit.html、

https://blog.csdn.net/lander_xiong/article/details/79018737



