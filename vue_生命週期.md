---
title: "vue"
date: "2020-01-13"
author: "vue"
summary: "介紹vue生命週期"
---



![vue](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vue.png)



以下說明，vue的生命週期期間，有哪些階段

每個階段先寫範例，在想要怎麼整個一起說明

找資料、整理文件、程式範例、文件總結說明整個生命週期

每個階段的用意是什麼?()

生命週期每個階段為function在執行



範例顯示的頁面

```php+HTML
<div id="app">
  {{ message }}
  <br/><br/>
  <button id="test" v-on:click="addtext()">新增文字</button>
</div>
<br/>
<button onclick="del()">銷毀</button>
```



## 1.beforeCreate

Vue Instance、 Component初始化，這時部份元素尚未被掛載，data還沒建立，所以data與el都會是undefined。

每個都要補用法?使用時機?

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    beforeCreate(){
        //這時message會等於undefined
    	console.log(this.message)
    }
})
```

demo:https://codepen.io/coolgood88142/pen/ExaOeNa?editors=1111



## 2.created

Vue Instance、 Component建立完成，data可取得資料，屬性可以賦予值，但是el屬性還沒建立。

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    created:{
        //console log會顯示123456，頁面卻只顯示123
        message += '456'
      	console.log(message)
      }
    }
})
```

demo:https://codepen.io/coolgood88142/pen/abzPWKL?editors=1111



## 3.beforeMount

el已建立，卻沒執行元素掛載。

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    beforeMount(){
        //這裡要用debug來看
        //console log會顯示123時，但是el已建立，但是元素未掛載，所以頁面上還是保持{{ message }}
      	console.log(this.message)
    }
})
```

demo:https://codepen.io/coolgood88142/pen/yLyGbrg?editors=1111



## 4.mounted

已執行元素掛載，這時data的資料才會顯示在頁面上。

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    methods:{
        addMessage:function(){
            this.message = 'mounted add ' + this.message
        }
    },
    mounted(){
        //當頁面上的元素掛載完之後，執行addMessage function，message就會變成mounted add 123
      	this.addMessage()
    }
})
```

demo:https://codepen.io/coolgood88142/pen/BayvRga



## 5.beforeUpdate

當資料更新會被呼叫使用，DOM物件卻沒更新

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    //message為abc時，brfore在console log看到message的值還是為123
    beforeUpdate(){
        let brfore = document.getElementById('app').innerHTML
      	console.log(brfore)
    }
})

app.message = 'abc'
```

demo:https://codepen.io/coolgood88142/pen/dyPwRYa?editors=1111



## 6.updated

資料更新完成後，已經更新DOM物件，顯示在頁面上

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    //message為abc時，updated在console log看到message的值為abc已經改變了
    updated(){
        let updated = document.getElementById('app').innerHTML
      	console.log(updated)
    }
})

app.message = 'abc'
```

demo:https://codepen.io/coolgood88142/pen/eYmbEzX

DOM為Document Object Model，在html裡每個節點皆為物件(Object)且擁有各自的屬性以及方法



beforeDestroy與destroyed要執行vm.$destroy()，才會執行

## 7.beforeDestroy

所有資料與屬性銷毀之前，都還可以使用

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    methods:{
        addtext:function(){
            this.message = this.message + '456'
		}
    },
    beforeDestroy(){
      	console.log('app銷毀之前')
    }
})

function del(){
    //brforeDestroy執行時，console log看到app銷毀之前
    app.$destroy();
    console.log(app.message)
}
```

demo:https://codepen.io/coolgood88142/pen/jOEoKax?editors=1111



## 8.destroyed

銷毀所有的資料與屬性，包含事件等等。

```javascript
let app = new Vue({
    el: '#app',
    data:{
        message: '123'
    },
    methods:{
        addtext:function(){
            this.message = this.message + '456'
		}
    },
    destroyed(){
      	console.log('app已經銷毀了')
    }
})

function del(){
    //destroyed執行時，app已經銷毀了
    app.$destroy();
}
```

demo:https://codepen.io/coolgood88142/pen/gObJKJX?editors=1111



## 總結

vue生命週期在於el與data的變化，beforeCreate、created在vue instance初始化才會執行，beforeMount、mounted在建立DOM物件才會執行，beforeUpdate、updated在data裡的資料變數的值改變時才會執行，beforeDestroy、destroyed在執行destroy function的時候才會執行



建立vue是一個生命週期，要了解什麼時候會結束

vue分為Instance、生命週期



### 問題：created是在data建立之後才有的，那為什麼要在這個階段執行初始化，直接在data裡建立不就好了?



### created與data建立變數的差異

created在這個階段時，el還沒建立，卻可以拿到data裡的變數value

created執行初始化時，可以拿到data裡的變數，建立新變數的預設值
created做完之後，data裡的變數，才可以做變更



參考資料:

https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram、

https://ithelp.ithome.com.tw/articles/10188213、

https://www.hellosanta.com.tw/blog/understanding_vuejs_lifecycle_hooks、

[https://blog.niclin.tw/2017/10/08/%E6%90%9E%E6%87%82%E7%94%9F%E5%91%BD%E9%80%B1%E6%9C%9F-lifecycle/](https://blog.niclin.tw/2017/10/08/搞懂生命週期-lifecycle/)、

https://cythilya.github.io/2017/04/11/vue-instance/、

[https://medium.com/change-or-die/javascript%E5%88%9D%E5%AD%B8-dom%E5%B8%B8%E7%94%A8%E5%B1%AC%E6%80%A7%E8%88%87%E6%96%B9%E6%B3%95-ef851afdb65a](https://medium.com/change-or-die/javascript初學-dom常用屬性與方法-ef851afdb65a)、

https://www.itread01.com/content/1550379391.html、

[https://medium.com/@yvonne11yuting/vue-%E9%81%8B%E4%BD%9C%E6%B5%81%E7%A8%8B%E5%9C%96-be7e2a5cde36](https://medium.com/@yvonne11yuting/vue-運作流程圖-be7e2a5cde36)



