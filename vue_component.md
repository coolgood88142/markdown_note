---
title: "vue"
date: "2020-01-09"
author: "vue"
summary: "介紹vue幾個用法"
---



使用時機:在程式裡什麼時候用這些方法?為什麼?(很多個分法確只用這個)



## emit

vue的component想將資料傳給instance時，要使用emit，自定義事件來觸發，將emit整個包出去，在component裡，監聽了emit的事件，當觸發時監聽到emit的事件去對應要執行function

emit適合在component想要透過事件傳值給instance，這樣每個component都可以用emit傳給同一個事件或者不同事件。

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

instance要將資料傳到vue的component，要使用props，必須先設定傳進來的值是什麼資料型態，接收到props的資料就會做更新。

props適合在instance有資料與component某個資料做同步更新。

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
    methods: {
        showText: function(){
            let show_Text =  'component_' + this.addtext
            alert(show_Text)
        }
    },
    template: '<button id="add_text" v-on:click="showText()">顯示props資料</button>'
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

computed是計算data裡的變數，當vue的頁面有需要被計算的時，可使用computed，computed分為get與set(讀取與設值)，如果沒寫預設是get，在component或是instance做計算時，我們也可以透過methods做計算，但是methods必須要觸發事件才可以做，如果為了計算資料，會需要一直觸發事件，只會讓程式碼感覺複雜，使用computed的話，只要變數資料改變就會自動更新，可減少資料重新運算的次數。

computed相依性很高，會隨著變數做更新，在頁面初始化時get會直接計算變數資料，如果computed計算後的變數資料被改變時，就會執行set，取得改變後的資料做計算，做完之後會執行get回傳到變數。



#### get流程

當網頁初始化時，會綁定computed變數名稱，去計算變數的值，所以任何function中有影響變數的運算，會自動重新計算，如果有加set就不會重新計算



#### set流程

當computed變數值改變時，會重新計算set裡的function中會包含newValue的參數，newValue是指變數的新的值



#### 什麼情況才要用到get與set?

get是computed的預設值，所以有使用computed就會執行get，set是計算中的變數有要做重新計算時才會需要。



#### function可以簡寫不加括號



要補上觸發流程順序(1.get 2.改變執行set且newValue的值怎麼來的)

補流程圖(有幾種流程，目前只知道兩種 1.頁面初始化 2.變數資料改變時)



敘述寫清楚一點，別人不知道計算後的值是指什麼?(computed?還是??)





計算的值為function

計算的值html裡，可以簡寫不加括號

範例的bug要做修正

get與set使用時機、使用方式、為什麼要用?(computed可以只寫get、什麼時候用set)



set裡的newValue要解釋為什麼會有這個?





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
                let blank_length = newValue.split(' ').length
                console.log(blank_length)
                if(blank_length == 1){
                    this.lastName = newValue.split(' ')[0]
                    this.firstName = newValue.split(' ')[1]
                }else{
                    alert('請輸入正確姓名!');
                    this.lastName = ''
                    this.firstName = ''
                }
            }
        }
    }
})
```

demo:https://codepen.io/coolgood88142/pen/BaNBYBg



## watch

watch是監聽某個值，當值做改變時，要去執行某些事件，在watch可以得到改變前與改變後的資料，監聽到之後執行事件，雖然也可以跟computed一樣做計算。

watch適合非同步更新變數資料時使用，相依性相較之下較低，可以做的事情較多。



原本watch在vue初始化時，是不會做監聽，但是加hender和immediate屬性可以執行完初始化時去執行，適合在

vue初始化時背後執行監聽，就可以先拿到預設值做計算。



immediate是vue初始化做完時立即執行

immediate(使用時機，什麼時候適用)

watch是監聽值，那監聽值是不是在初始化時就需要用到







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
        // count(newVal, oldVal){
        // this.be_count = oldVal
        // this.af_count = newVal
        // }
        count:{
          handler(newVal, oldVal){
            this.be_count = oldVal
            this.af_count = newVal
          },
          immediate: true
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



