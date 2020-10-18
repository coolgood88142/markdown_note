---
title: "ESLint "
date: "2020-08-02"
author: "ESLint"
summary: "說明如何使用ESLint，將程式碼調整"
categories: ["ESLint"]
tags: ["ESLint","ESLint"]
---

ESLint是code style的檢查工具，能確保你的程式碼品質，在團隊中需要用code style工具，統一程式碼

#### 安裝ESLint

```
npm install -g eslint
```

裝完之後，執行初始化

```
eslint --init
```

系統會問一些問題

How would you like to configure ESLint?

請問要怎麼配置ESLint? 這裡選擇`Use a popular style guide`

- Answer questions about your style

- Use a popular style guide

- Inspect your JavaScript file(s)

  

ESLint的優點

- 找出語法錯誤

  沒宣告變數就拿來用、少了括號等等常見的語法錯誤

- Airbnb

於 ES6 該怎麼學比較好!?很建議安裝 ESLint 來邊學邊修正觀念。ESLint 是一個團隊統一程式碼結構的工具，如果程式碼不符合規範，則會出現相對應的提示，而其中有三大主流規範是許多開發者愛用的：

- Google
- Airbnb
- JavaScript Standard Style

Airbnb 是其中一個最流行的 JavaScript 代碼規範，它差不多包含所有 JavaScript 的角度。它也是我的個人項目所使用的代碼規範。如果你的項目是基於 React 的，那麼你可以選擇安裝 [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)，或者你可以選擇最基本的 [eslint-config-airbnb-base](https://www.npmjs.com/package/eslint-config-airbnb-base)。

[eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb) 包含 ECMAScript 6 + 以及 React 的 ESLint 代碼規範。在安裝 `eslint-config-airbnb` 的時候，它會一同安裝 `eslint`, `eslint-plugin-import`, `eslint-plugin-react`, and `eslint-plugin-jsx-a11y`。如果你的項目不是 React 的話，那麼你可以選擇`eslint-config-airbnb-base`。

```
npm install eslint-config-airbnb
```

[eslint-config-airbnb-base](https://www.npmjs.com/package/eslint-config-airbnb-base) 包含ECMAScript 6 + 的 ESLint 代碼規範。安裝它的時候，它會需要 `eslint` 和 `eslint-plugin-import`。

```
npm install eslint-config-airbnb-base
```

然後在你的 `.eslintrc` 加入 `"extends": "airbnb-base"` 就可以了。

Airbnb 的完整代碼規範可以在 [airbnb/javascript](https://github.com/airbnb/javascript) 上閱讀。



- 不會再修改的請用 `const`
- 能寫單行請別寫那麼多行好嗎？
- 一個參數而已，幹麻用小括號包起來
- 每行後面都要有分號
- `console.log` 記得移除