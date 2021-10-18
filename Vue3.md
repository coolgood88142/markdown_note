## Vue3

vue3是vue最近推出的新的版本，隨著vue程式庫的演變與架構才衍生出vue3

### vue2與vue3的差異

他們最大的差異是生命週期整個改寫

- #### setup

  vue在做初始化時beforeCreate和created，會先建立好建構值，vue3改用setup來定義，生命週期與function都要由這裡來定義

  ![vue3-2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vue3-2.png>)

- #### reactive

  可以定義一個物件或是陣列，透過reactive才能響應式的偵測資料的變動，要將data定義的資料都寫在這裡，然後再用import去拿資料

  ![vue3-3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vue3-3.png>)

- #### Lifecycle Hooks

  vue的mounted、beforeDestory等等，在vue3全部以on開頭的Hooks，例如：onMounted、onBeforeUnmount等等，並且要將生命週期要寫在setup函式裡面。

  ![vue3-4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vue3-4.png>)

  ​	![vue3-1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vue3-1.png>)

- emit and props

  vue之前寫法是用this，現在取而代之改用context，但而是要放到setup去定義

  ```vue
  //vue2
  this.$emit('changePage', 5);
  
  //vue3
  context.$emit('changePage', 5);
  ```

- Fragments

  vue之前有要顯示在template裡，可能每定一個元素就放到裡面，現在vue3可以直接定義要放什麼元素

  ![vue3-6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vue3-6.png>)

- ref

  只能監聽String 、Number、 Boolean的變化，當Array、 Object 內部屬性有變化是沒有辦法監聽的(請用reactive)

  在setup裡面ref會回傳一個物件，所以必須用value屬性來取到值，而在template ref會自動unwraps就可以直接取用， 不需要value

  假如是要取得DOM的話

  1. 先在setup定義一個ref
  2. 綁定在想要取得的DOM上
  3. 就可以在onMounted階段取得DOM

  ![vue3-7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/vue3-7.png>)

### 參考資料

https://medium.com/coding-hot-pot/%E5%9C%A8%E9%96%8B%E5%A7%8B%E4%B9%8B%E5%89%8D-%E6%88%91%E6%87%89%E8%A9%B2%E5%85%88%E4%BA%86%E8%A7%A3%E4%BD%A0-%E8%81%8A%E8%81%8Avue-3%E7%9A%84%E6%94%B9%E5%8B%95-31864be9c237

https://cloud.tencent.com/developer/article/1741250

https://penueling.com/%E6%8A%80%E8%A1%93%E7%AD%86%E8%A8%98/vue3-%E7%9A%84%E8%B3%87%E6%96%99%E7%8B%80%E6%85%8B%E7%AE%A1%E7%90%86%EF%BC%8Cprovide-inject%E3%80%81vuex/