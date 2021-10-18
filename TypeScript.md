## TypeScript

### 什麼是TypeScript?

由 Microsoft 主打，擁有型別系統（Type System）與介面（Interface）設計的語言，在原生 JS 上包裝上一層新的語法，因此如果在 TypeScript 裡面編譯原生 JS 也是可以動作的，只不過有可能發生錯誤的原因是：**TypeScript 會幫你把原生 JS 裡潛藏的 Bug 或者是沒有補清的邏輯拋出警訊（Warning）的動作**。

### Type System

可以減少bug容易維護

- 變數名稱：系統會自動改成駝峰式寫法

- 避免類別或物件方法發生錯誤：定義某類別必須包含 A 以及 B 方法，但是方法的型別也有很多種類，有時候需不需要回傳新的值也是一個問題。 TypeScript 會自動幫我們確認要回傳的型別是否是一樣的。

- 協助開發： TypeScript 在 IDE 上的 Autocomplete Feature 來協助開發並且防止錯誤

  ![typescript1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/typescript1.png>)

  ![typescript2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/typescript2.png>)

### 安裝

用npm安裝typescript套件

```bash
npm install -g typescript
```

在指定的資料夾中下初始化

```bash
tsc --init
```

### ECMAScript 協作

TypeScript 是藉由型別系統以及擴充ECMAScript正式定義的規範使得我們可以寫出更多漂亮的語法。

ECMAScript 是原生JS的擴充，而 TypeScript 又是結合部分 ES Feature 以及型別系統的語言，如果有使用ES5 、ES6 ，**並不是所有的 ES Feature 都有被 TS 支援。不過基本上，大部分常用的 ES6 Feature 都可以使用**！

基本用法：

- `let` 與 `const` 以及其變數作用域
- 解構語法（Destructuring）
- 箭頭函式（Arrow Functions）
- 模板字串（Template Literals）
- 匯集-展開 運算子（Rest-Spread Operator）
- 常用的 ES6 以及以後的語法等

### 參考資料

https://ithelp.ithome.com.tw/articles/10214714

https://www.wikiwand.com/en/ECMAScript

