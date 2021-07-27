## 實做MySQL-宣告變數及其應用

我們使用SQL想要用查詢做運算，還要存取資料，會使用宣告變數，可以用變數當條件式、做if判斷、計算資料等等，來額外建立新的資料範圍

- 優點：它是全域變數，可以在SQL檔案裡任一個地方，都可以組條件或者做判斷使用

- 缺點：如果太多了變數做計算，執行時間會變很長


#### 為什麼要使用?

如果想要新增一個假的欄位資料，但是又不想重新規劃table結構，這時就可以用宣告變數，來新增假的資料，資料來源可以從別的table，當條件使用或者直接寫死，要設定欄位為預設值。

應用場景：用範例來說為什麼要使用?

去查看laravel database實際寫範例使用宣告變數

mysql的宣告變數分兩種，內部宣告、外部宣告

- 內部宣告

  @xxx := xxx 這裡寫法就算是內部宣告，宣告變數等於什麼值，要指定變數來自於某個table資料，就使用內部宣告，來新增假資料

  ```sql
  SELECT 
  	id, title, created_at, rank 
  from 
  	(SELECT post_tmp.id, post_tmp.title, post_tmp.created_at, @rownum := @rownum+1 ,
       IF(@title = post_tmp.title, @rank := @rank+1, @rank := 1) AS rank,
       @title := post_tmp.title 
       	from 
       		(SELECT @rownum := 0, @title := NULL , @rank := 0) AS a ,
       		(select id, title, created_at 
               	FROM post 
               		ORDER BY id ASC ,created_at DESC ) post_tmp ) AS result;
  ```

  以上範例，查詢post的id、 title,、created_at,、rank，4個欄位做出排名，用post跑迴圈，

  使用@rownum設定每筆資料筆數，@title來判斷post_tmp的title是否為一樣，@rank回傳rank欄位的值，

  去了解每個變數為什麼會寫在那個位置?

  去確認SQL每航在做什麼? 不要忽略，要思考在幹嘛

  

  @rownum是指經歷每筆資料加1，判斷@title變數跟title的資料一樣時，@rank就遞增，沒有就回傳1，@title等於title的資料。再post要先以id由小到大做排序，created_at由大到小，這時的post資料，查詢每筆資料以相同的title資料做條件，並且用rank變數做遞增

  ![mysql2](C:\xampp\htdocs\markdown_note\assets\images\mysql2.PNG)

- 外部宣告

  用set 命名變數後，設定預設值，如果變數想要應用再多個table做查詢，就用外部宣告來多加一個欄位
  
  ```php
  set @a = 'test';
  SELECT *, @a FROM `post`;
  ```

  以上範例，查詢post所有資料，這時@a變數宣告了一個test文字，加在post的查詢欄位裡，執行查詢後會顯示所有post資料，有多一個欄位為@a，資料為test
  
  ![mysql1](C:\xampp\htdocs\markdown_note\assets\images\mysql1.PNG)

參考資料：

- https://pclevinblog.pixnet.net/blog/post/314563150-%5Bmysql%5D2-15-%E5%AE%A3%E5%91%8A%E8%AE%8A%E6%95%B8%E5%8F%8A%E5%85%B6%E6%87%89%E7%94%A8
- https://www.delftstack.com/zh-tw/howto/mysql/mysql-declare-variable/