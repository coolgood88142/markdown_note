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







## 1.beforeCreate

Vue Instance、 Component初始化，這時部份元素尚未被掛載，data還沒建立，所以data與el都會是undefined。

```

```



## 2.created

Vue Instance、 Component建立完成，data可取得資料，屬性可以賦予值，但是el屬性還沒建立。

你不知道每個階段的範例怎麼寫嗎?

程式範例:在data設定message值為123，當執行created時，在console.log更新message的值，就能做驗證

```

```



## 3.beforeMount

執行元素掛載之前，el還沒建立。

```

```



## 4.mounted

已執行元素掛載之後，el屬性已建立。

```

```



## 5.brforeUpdate

當資料更新會被呼叫使用，這時不會顯示在頁面上

```

```



## 6.updated

資料更新完成後，顯示在頁面上

```

```



## 7.brforeDestroy

所有資料與屬性銷毀之前，都還可以使用

```

```



## 8.destroyed

銷毀所有的資料與屬性，包含事件等等。

```

```



建立vue是一個生命週期，要了解什麼時候會結束

vue分為Instance、生命週期



Instance做一個文件

Instance:el、data、methods、computed、watch(不止這5個要找)

上面5項要說明與範例

Instance-methods、Instance-Properties，每一個子項目都用有說明(全部都要)



生命週期:brforeCreate、created、brforeMount、mounted...等等

模板語法:v-model、v-if、v-on

component:emit、props

Instance每個操作，要了解在生命週期什麼時候做(ex:computed不管什麼時候，只要資料改變就執行，所以會在updated)



總結說明生命週期



參考資料:

https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram、

https://ithelp.ithome.com.tw/articles/10188213、

https://www.hellosanta.com.tw/blog/understanding_vuejs_lifecycle_hooks、

[https://blog.niclin.tw/2017/10/08/%E6%90%9E%E6%87%82%E7%94%9F%E5%91%BD%E9%80%B1%E6%9C%9F-lifecycle/](https://blog.niclin.tw/2017/10/08/搞懂生命週期-lifecycle/)



