---
title: "Mongodb"
date: "2020-10-18"
author: "Mongodb"
summary: "說明如何使用Mongodb"
categories: ["Mongodb"]
tags: ["Mongodb"]
---

### Mongodb

#### insert(新增資料)

新增是用insert，將資料以key value的方式，新增欄位與資料

db.COLLECTION_NAME.insert(document)

```javascript
db.getCollection('keyword').insert({
	'en': 'epidemic',
	'tc': '疫情',
	'created_at': '2020-10-17 15:30:00',
	'update_at': '2020-10-17 15:30:00'
})
```

ex：新增en欄位資料為epidemic，tc欄位資料為疫情，created_at、update_at取現在時間

laravel的做法是用save儲存資料

```php
public function addKeywordData(Request $request)
{
    $en = $request->en;
    $tc = $request->tc;
    $datetime = Carbon::now()->toDateTimeString();
    $status = 'success';
    $message = '新增成功!';

    try {
        $keyword = new Keyword();
        $keyword->en = $en;
        $keyword->tc = $tc;
        $keyword->created_at = $datetime;
        $keyword->updated_at = $datetime;
        $keyword->save();
    } catch (Exception $e) {
        echo $e;
    }
        
    return [ 
        'status' => $status,
        'message' => $message 
    ];
}
```





#### update(更新資料)

更新是用update，指定某個資料做新增

db.COLLECTION_NAME.update(SELECTIOIN_CRITERIA, UPDATED_DATA)

update為什麼不能針對一個欄位做更新，去找找看

```javascript
db.getCollection('keyword').update({'en':'epidemic'},{$set:{'en':'epidemics', 'update_at': '2020-10-17 16:00:00'}})
```

ex：所有的en欄位資料為epidemic，更新en欄位資料為epidemics，update_at更新現在時間。

update還有另一種寫試save

db.COLLECTION_NAME.save({_id:ObjectId(),NEW_DATA})

save是取代指定_id的所有資料

```javascript
db.getCollection('keyword').save("_id" : ObjectId('5f778ca4610c000032007726'),{'en': now, 'tc': '現在'})
```

ex：_id的5f778ca4610c000032007726這筆資料，所有欄位取代成'en': now, 'tc': '現在'

laravel的做法是用save更新資料

```php
public function updateKeywordData(Request $request)
{
    $id = $request->id;
    $en = $request->en;
    $tc = $request->tc;
    $datetime = Carbon::now()->toDateTimeString();
    $status = 'success';
    $message = '更新成功!';    

    try {
        $keyword = Keyword::find($id);
        $keyword->en = $en;
        $keyword->tc = $tc;
        $keyword->updated_at = $datetime;
        $keyword->save();
        
    } catch (Exception $e) {
         echo $e;
    }
        
    return [
        'status' => $status,
        'message' => $message 
    ];
}
```



#### delete(刪除資料)

刪除是用remove，remove有兩個參數，第一個刪除指定資料，第二個是刪除第一筆，資料為true或1，沒寫會將移除全部的指定資料

db.COLLECTION_NAME.remove(DELLETION_CRITTERIA, true or 1)

```javascript
db.getCollection('keyword').remove({'en':'epidemic'}, true)
```

ex：移除en欄位資料的epidemic，沒寫true會移除

laravel的做法是用delete刪除資料

```php
public function deleteKeywordData(Request $request)
{
    $id = $request->id;
    $status = 'success';
    $message = '刪除成功!';

    try {
        $keyword = Keyword::find($id);
        $keyword->delete();

    } catch (Exception $e) {
        echo $e;
    }
        
    return [
        'status' => $status,
        'message' => $message
    ];
}

```

