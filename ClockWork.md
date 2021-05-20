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

### 4.範例

#### Logging

```php
User::create(clock($request->all()))
```

#### Timeline

```php
clock()->event('Importing tweets')->color('purple')->begin();
```

參考資料:

https://github.com/itsgoingd/clockwork、https://chrome.google.com/webstore/detail/clockwork/dmggabnehkmmfmdffgajcflpdjlnoemp?hl=zh-TW

