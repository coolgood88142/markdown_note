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

選擇Use a popular style guide，會顯示三個主流規範，這裡選擇`Airbnb`

- Google
- Airbnb：如果選擇此選項，會多問一個是否使用 React
- Standard

安裝完之後，專案上會多一個eslintrc.js檔案，eslint會依照檔案去檢查程式碼

檔案中的[rules](https://eslint.org/docs/rules/)可以設定檢查規則

例如：indent: "tab"，是指程式碼要用不能有空格

```javascript
module.exports = {
	env: {
		browser: true,
		es2021: true,
	},
	extends: [
		"plugin:vue/essential",
		"airbnb-base",
	],
	parserOptions: {
		ecmaVersion: 12,
		sourceType: "module",
	},
	plugins: [
		"vue",
	],
	rules: {
		indent: [
			"error",
			"tab",
		],
		"no-tabs": 0,
		"linebreak-style": [
			"error",
			"windows",
		],
		quotes: [
			"error",
			"double",
		],
		semi: [
			"error",
			"never",
		],
	},
};

```

#### 程式碼出現的問題

- var改用let或const
- let 的變數值不會更改，請改用const
- 字串用雙引號，請改用單引號
- 不使用全域變數、建議使用 `===`而不是 `==`
- console.log 沒移除
- space改用tab

Airbnb 是其中一個 JavaScript 代碼規範，在安裝過程中選擇Airbnb，eslintrc.js會寫入`extends: "airbnb-base"`，Airbnb 的完整代碼規範可以[參考](https://github.com/airbnb/javascript)。



參考資料：https://www.jianshu.com/p/05a6d7980a33、

https://ithelp.ithome.com.tw/articles/10195212