---
title: "vue"
date: "2020-01-09"
author: "vue"
summary: "說明vue流程"
---



## Vue-Componentes常用的兩種傳遞方式

**city.blade.php**

```php+HTML
<div id="app">
    @{{ message }}
    <counties_select v-on:change-counties="updateDistricts"></counties_select>
    <districts_select v-on:change-districts="getDistrictsSelected" :counties_selected="countiesSelected"></districts_select>
</div>
<script src="{{mix('js/app.js')}}"></script>
<script src="{{mix('js/city.js')}}"></script>
```

以下範例有用到emit與props兩種傳地方式，`city.js`拆開講解



### 1.emit

**counties.vue**

```vue
<template>
     <select v-model="counties_selected" id="counties" @change="$emit('change-counties', counties_selected)">
        <option value="" disabled selected>--請選擇--</option>
        <option v-for="(counties, index) in counties_array" :key="index" :value="counties.value">{{ counties.text }}</option>
    </select>
</template>

<script>
let counties_data =  [
    { text: '台北市', value: 0 },
    { text: '基隆市', value: 1 }
];

export default {
    data:function(){
        return {
            counties_array: counties_data,
            counties_selected: ''
        }
    }
}
</script>
```



**city.js**

```javascript
import counties from './components/counties.vue';
import districts from './components/districts.vue';
let app = new Vue({
    el: '#app',
    data:{
        message: 'Vue練習:',
        countiesSelected: NaN,
        districtsSelected: ''
    },
    components:{
        'counties_select': counties,
        'districts_select': districts
    },
    methods: {
        getDistrictsSelected(DistrictsSelected) {
            this.districtsSelected = DistrictsSelected;
        },
        updateDistricts(CountiesSelected) {
            this.countiesSelected = CountiesSelected
        }
    }
})
```

counties的componentes為下拉選單，綁定了counties_selected屬性。

當選擇資料，使用emit將參數，整個包起來傳到外面去，名稱為change-counties。
外面的city.blade接收時，change-counties去監聽包出去的事件，這時會執行showCounties，CountiesSelected就是emit帶來的參數。



### 2.props

**districts.vue**

```vue
<template>
    <select v-model="districts_selected" id="districts" @change="$emit('change-districts', districts_selected)">
        <option value="" disabled selected>--請選擇--</option>
        <option v-for="(districts, index) in districts_array" :key="index" :value="districts.value">{{ districts.text }}</option>
    </select>
</template>

<script>
let districts = [
    [
        { text: '中正區', value: 100 }, { text: '大同區', value: 103 }, { text: '中山區', value: 104 }, { text: '松山區', value: 105 },
        { text: '大安區', value: 106 }, { text: '萬華區', value: 108 }, { text: '信義區', value: 110 }, { text: '士林區', value: 111 },
        { text: '北投區', value: 112 }, { text: '內湖區', value: 114 }, { text: '南港區', value: 115 }, { text: '文山區', value: 116 }
    ],
    [
        { text: '仁愛區', value: 200 }, { text: '信義區', value: 201 }, { text: '中正區', value: 202 }, { text: '中山區', value: 203 },
        { text: '安樂區', value: 204 }, { text: '暖暖區', value: 205 }, { text: '七堵區', value: 206 }
    ]
];

export default {
    props: {
        counties_selected:{
            type:Number
        }
    },
    computed: {
        districts_array(){
            if (this.counties_selected != NaN ){
                this.districts_selected = '';
                return districts[this.counties_selected];
            }
        }
    },
    data:function(){
        return {
            districts_selected: ''
        }
    }
}
</script>>
```

當countiesSelected改變時，從`city.js`傳到`city.blade.php`的`districts_select`裡的counties_selected。

這時districts的componentes，props接收counties_selected資料，computed在計算districts_array資料，就可使用countiesSelected組districts_array資料，回傳到選單上即時更新。



參考資料:

https://dotblogs.com.tw/wasichris/2017/03/04/021726、

https://chenuin.blogspot.com/2019/01/vuejs-propsemit.html



