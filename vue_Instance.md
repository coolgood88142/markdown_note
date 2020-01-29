---
title: "vue"
date: "2020-01-19"
author: "vue"
summary: "介紹vue的Instance"
---

Instance可分為el、data、methods、computed、watch

Instance:el、data、methods、computed、watch(不止這5個要找)

上面5項要說明與範例

Instance-methods、Instance-Properties，每一個子項目都用有說明(全部都要)



## el

el為html的屬性，主要是在哪個屬性執行vue

```javascript
let app = new Vue({
    el: '#app'
})
```



## data

data為vue的資料，指定key要放什麼資料變數

```javascript
let app = new Vue({
    el: '#app',
    data: {
    	message: '點擊次數:'
        count: 0
    } 
})
```



## methods

methods是vue的所有function的集合

```javascript
let app = new Vue({
    el: '#app',
    data: {
    	message: '點擊次數:'
        count: 0
    },
    methods:{
        add: function () {
            this.count++
		}
    }
})
```



## computed

computed為計算屬性，當vue的頁面有需要被計算的屬性時，可使用computed，computed分為get與set(讀取與設值)，如果沒寫預設是get，在component或是instance，可以在內部資料做計算，但是我們也可以透過methods做計算，methods必須要觸發事件才可以做，如果為了計算屬性資料，會需要一直觸發事件，只會讓程式碼感覺複雜，使用computed的話，只要變數的屬性資料改變就會自動更新，可減少資料重新運算的次數。

computed相依性很高，會隨著變數做更新，在一開始頁面時就會執行get，直接回傳資料，當變數更新時會執行set及時更新資料。



以下範例，firstName與lastName帶姓名的預設值， computed的computed_message會執行getter，就會顯示`王 曉明`，當computed_message改變時，computed會執行setter，firstName與lastName就會及時更新

```php+HTML
<div id="app">
    請輸入姓名(請用空白區分):<input v-model="computed_message">
    <br/><br/>
    <p>姓氏:{{ lastName }}</p>
    <p>名字:{{ firstName }}</p>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js"></script>
```



```javascript
let app = new Vue({
    el: '#app',
    data:{
        firstName: '曉明',
        lastName: '王',
    },
    computed:{
        computed_message:{
            get: function(){
                return this.lastName + ' ' +this.firstName
            },
            set: function(newValue){
                this.lastName = newValue.split(' ')[0]
                this.firstName = newValue.split(' ')[1]
            }
        }
    }
})
```

demo:https://codepen.io/coolgood88142/pen/gObdYzM



## watch

watch是監聽某個值，當值做改變時，要去執行某些事件，在watch可以得到改變前與改變後的資料，監聽到之後執行事件，雖然也可以跟computed一樣做計算。

watch適合非同步更新變數資料時使用，相依性相較之下較低，可以做的事情較多。



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



生命週期:brforeCreate、created、brforeMount、mounted...等等

模板語法:v-model、v-if、v-on

component:emit、props

Instance每個操作，要了解在生命週期什麼時候做(ex:computed不管什麼時候，只要資料改變就執行，所以會在updated)





## Instance-methods

## Instance-Properties



參考資料:



