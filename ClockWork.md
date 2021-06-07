---
title: "ClockWork"
date: "2021-05-09"
author: "ClockWork"
summary: "說明使用ClockWork 做debugger"
categories: ["debugger"]
tags: ["ClockWork","debugger"]
---

**ClockWork**  。

### 1.安裝**[ClockWork](https://github.com/itsgoingd/clockwork)**套件

用composer 安裝

```javascript
$ composer require itsgoingd/clockwork 
```

### 2.設定app.php

```javascript
'aliases' => [
	'Clockwork' => Clockwork\Support\Laravel\Facade::class,
]
```

### 3.在Chrome安裝[Clockwork](https://chrome.google.com/webstore/detail/clockwork/dmggabnehkmmfmdffgajcflpdjlnoemp?hl=zh-TW) 擴充功能

安裝之後，server啟動之後，開chrome按F12，程式中有使用clockwork的function，會顯示執行時花了多少時間

![clockwork1](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/clockwork1.PNG)

### 4.記錄

#### Logging

- 記錄任意的變數、陣列、物件

```php
clock(User::first(), auth()->user(), $username)
```

- clock()函式記錄第一個參數花了多少時間

```
User::create(clock($request->all()))
```

- 使用info可以記錄到log裡

```
clock()->info("User {$username} logged in!")
```

#### Timeline

- 建立事件記錄程式碼執行時間，從begin到end的範圍內，記錄執行程式碼花了多少時間
  - color是在瀏覽器顯示Importing tweets，這個事件用紫色來標記

```php
clock()->event('Importing tweets')->color('purple')->begin();
...自己的程式碼
clock()->event('Importing tweets')->end();
```

- 承上，事件名稱可以自行定義，例如importing-tweets-1

```
foreach ($users as $i => $user) {
    clock()->event('Importing tweets')->name("importing-tweets-{$i}")->begin();
    ...
    clock()->event("importing-tweets-{$i}")->end();
}
```

#### 5.函式

- `all()` 回傳所有存取的資料

```php
clock->all($user)
```

- `find($id)` 回傳指定單一個資料

```php
clock->find($userid)
```

- `latest()` 回傳最新的資料

```php
clock->latest($user)
```

- `previous($id, $count = null)` 回傳指定id前的資料，並且限制前幾筆

```php
clock->previous($userid, 1)
```

- `next($id, $count = null)` 回傳指定id後的資料，並且限制前幾筆

```php
clock->next($userid, 1)
```

- `store(Request $request)` 請求存取

```php
clock->store($request)
```

- `cleanup()` 清除存取

```php
clock->store()
```

執行完之後，在laravel專案底下storage/clockwork中產生json檔，會記錄時間、router、header等等



#### 用npm安裝

可以用npm安裝clockwork

- Web引用js

```javascript
<script src="https://cdn.jsdelivr.net/gh/underground-works/clockwork-browser@1/dist/metrics.js"></script>
<script src="https://cdn.jsdelivr.net/gh/underground-works/clockwork-browser@1/dist/toolbar.js"></script
```

- 安裝clockwork-browser

```
npm install clockwork-browser
```

- 安裝之後，要在app.js要新增下面這段

```
import 'clockwork-browser/metrics'
import 'clockwork-browser/toolbar'
```

- 在執行編譯

```
npm run watch
```

參考資料:

https://github.com/itsgoingd/clockwork、https://chrome.google.com/webstore/detail/clockwork/dmggabnehkmmfmdffgajcflpdjlnoemp?hl=zh-TW

