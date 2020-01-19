---
title: "vue"
date: "2020-01-09"
author: "vue"
summary: "介紹vue幾個用法"
---



使用時機:在程式裡什麼時候用這些方法?為什麼?(很多個分法確只用這個)



## emit

vue的component想將資料傳給instance時，要使用emit，自定義事件來觸發，將emit整個包出去，在component裡，監聽了emit的事件，當觸發時監聽到emit的事件去對應要執行function

```javascript
$emit('事件名稱',傳遞參數) //參數可帶或不帶
```



以下範例，component名稱為addmessage，emit事件為add-text帶參數text，頁面上的addmessage監聽add-text事件，要執行addText function。

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

demo：https://codepen.io/coolgood88142/pen/LYErYgQ



## props

instance要將資料傳到vue的component，要使用props，必須先設定傳進來的值是什麼資料型態，接收到props的資料就會做更新

```html
<component :addtext="message"></component>
```



以下範例，文字框中綁定了message，資料帶`Hello Vue!`，component名稱為addname，建立props名稱為addtext資料型態為字串，當message的資料做改變時，props的addtext資料就會跟著更新。

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
    computed: {
      component_text(){
        return  'component_' + this.addtext
      }
    },
    template: '<p>Props文字：{{ component_text }}</p>'
})

let app = new Vue({
    el: '#app',
    data:{
        message: 'Hello Vue!'
    }
})
```

demo：https://codepen.io/coolgood88142/pen/qBEKYjy



## computed

computed為計算屬性，分為get與set(讀取與設值)，如果沒寫預設是get，在component或是instance，可以在內部資料做計算，資料改變時computed就會執行計算，做完後回傳資料

computed是計算需要被計算的屬性，當頁面有個要計算的屬性時，頁面的上有個v-bind綁訂了一些變數，但是有個變數是要被計算，可以直接寫total等於加減乘除，但這樣在程式會太複雜難維護，所以我們可以透過methods或computed，methods需要透過事件才會執行，但是computed不用，computed可做到即時更新，只要變數改變就會更新計算的值

computed相依性很高，而methods沒差別，methods透過觸發事件才會去改變變數或著不改變，computed在一開始秀頁面時就會執行，是直接回傳變數是什麼值



要找setter什麼時候在computed執行?





(什麼時候才會做computed? 跟data有什麼差?改變也不一定在computed可以在methods)

範例無法驗證是從computed執行，跟v-model改變值有什麼差?

以下範例，message預設值為`Hello computed`，computed有個computed_message會執行getter讀取message，所以一樣顯示`Hello computed`，當message做改變時，computed會執行setter，computed_message就會跟message一樣

```php+HTML
<div id="app">
    <input v-model="message">
    <br/><br/>
    <p>{{ computed_message }}</p>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js"></script>
```



```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: 'Hello computed'
    },
    computed:{
        computed_message:{
            get: function(){
                return this.message
            },
            set: function(newValue){
                this.message = newValue
            }
		}
    }
})
```

demo:https://codepen.io/coolgood88142/pen/gObdYzM



## watch

watch是監聽某個值，當值做改變時，要去執行某些事件，可以在watch得到改變前與改變後的資料

為什麼要改變前?要幹嘛?不要有什麼差?

(computed很像，但是computed主要是計算，watch只是監聽後要做什麼事情，程式雖然兩個可以做到一樣的事情，但是有差別)



以下範例，count預設值帶1，be_count與af_count帶空字串，當按下按鈕時執行add function，count就會遞增，這時watch監聽到的count，得到改變前與改變後的資料，在分別顯示在be_count與af_count

```php+HTML
<div id="app">
    <p>目前數字:{{ count }}</p>
    <p>改變前count:{{ be_count }}</p>
    <p>改變後count:{{ af_count }}</p>
    <button @click="add()">新增次數</button>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js"></script>
```



```javascript
let app = new Vue({
    el: '#app',
    data:{
        count: 1,
        be_count:'',
        af_count:''
    },
    methods:{
        add: function () {
            this.count++
		}
    },
    watch:{
        count(newVal, oldVal){
            this.be_count = oldVal
            this.af_count = newVal
		}
    }
})
```

demo:https://codepen.io/coolgood88142/pen/PowdoNK



參考資料:

https://dotblogs.com.tw/wasichris/2017/03/04/021726、

https://chenuin.blogspot.com/2019/01/vuejs-propsemit.html、

https://blog.csdn.net/lander_xiong/article/details/79018737、

https://pjchender.blogspot.com/2017/05/vue-computed-getter-setter.html、

https://ithelp.ithome.com.tw/articles/10185324、

https://cythilya.github.io/2017/04/15/vue-watch/



