## 實做MySQL-宣告變數及其應用

我們使用SQL想要用查詢做運算，還要存取資料，會使用宣告變數，可以用變數當條件式、做if判斷、計算資料等等，來額外建立新的資料範圍

- 優點：它是全域變數，可以在SQL檔案裡任一個地方，都可以組條件或者做判斷使用，也可以用

- 缺點：如果太多了變數做計算，執行時間會變

  為什麼要使用?

  補充兩種宣告變數的差異

  內部宣告的情境、描述

  外部宣告的情境、描述

mysql的宣告變數分兩種，內部宣告、外部宣告

- 內部宣告

  @xxx := xxx 這裡寫法就算是內部宣告，宣告變數等於什麼值，如果想要將資料

  ```sql
  SELECT 
  	id, title, created_at, rank 
  from 
  	(SELECT post_tmp.id, post_tmp.title, post_tmp.created_at, @rownum := @rownum+1 ,
       IF(@pdept = post_tmp.title, @rank := @rank+1, @rank := 1) AS rank,
       @pdept := post_tmp.title 
       	from 
       		(SELECT @rownum := 0, @pdept := NULL , @rank := 0) AS a ,
       		(select id, title, created_at 
               	FROM post 
               		ORDER BY id ASC ,created_at DESC ) post_tmp ) AS result;
  ```

  以上範例，查詢post的id、 title,、created_at,、rank，4個欄位做出排名，post要先以id由小到大做排序，created_at由大到小，這時的post資料，查詢每筆資料以相同的title資料做條件，並且用rank變數做遞增

  ![mysql2](C:\xampp\htdocs\markdown_note\assets\images\mysql2.PNG)

- 外部宣告

  ```php
  set @a = 'test';
  SELECT *, @a FROM `post`;
  ```

  以上範例，查詢post所有資料，這時@a變數宣告了一個test文字，加在post的查詢欄位裡，執行查詢後會顯示所有post資料，有多一個欄位為@a，資料為test

  ![mysql1](C:\xampp\htdocs\markdown_note\assets\images\mysql1.PNG)

參考資料：

- 
- https://www.delftstack.com/zh-tw/howto/mysql/mysql-declare-variable/