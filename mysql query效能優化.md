## mysql query效能優化

#### 目錄

- 優化查詢
- 盡可能不使用函式
- select避免使用不必要查詢欄位
- 盡可能不使用DISTINCT和UNION
- 盡可能用INNOR JOIN，不要用LEFT JOIN或RIGHT JOIN
- 避免在 where 子句中對欄位進行 null 值判斷
- 分頁查詢
- union all盡量代替union
- 使用連接（JOIN）來代替子查詢(Sub-Queries)
- 使用聯合(UNION)來代替手動創建的臨時表
- 使用index查詢

#### **優化查詢**

可以用WHERE、JOIN、ORDER BY 和GROUP BY，針對某個欄位做查詢

#### 盡可能不使用函式

有建立索引值的情況下，使用函式的話就不會用索引做查詢，會增加查詢時間

#### **select避免使用不必要查詢欄位**

不要使用select * ，最好是能指定欄位，才可以快速找到欄位資料，如果有10萬筆資料，因為有多餘的欄位，在查詢時時間會過長

#### 盡可能不使用DISTINCT和UNION

DISTINCT和UNION，在使用時會導致不必要的排序和 SQL 執行速度減慢

#### 盡可能用INNOR JOIN，不要用LEFT JOIN或RIGHT JOIN

使用外部連接時，會讓效能會很差，還會限制 MySQL 查詢選項，導致 SQL 語句執行速度變慢。

#### 避免在 where 子句中對欄位進行 null 值判斷

如果使用索引值的話，SQL不會優先從索引值尋找，會直接找欄位值

最好的方式是直接搜尋欄位的預設值

#### 分頁查詢

查詢時最好可以限制第N筆至第N筆的資料，而不是用limit

#### union all盡量代替union

union all是合併後在進行過濾，會影像到排序，增加CPU運算，消耗大量資源及延遲，如果用union是會直接過濾

#### **使用連接（JOIN）來代替子查詢(Sub-Queries)**

如果想要額外查詢別的table欄位資料，盡量不使用join，可以額外組子查詢，這樣不會再額外去拿join的table資料

#### **使用聯合(UNION)來代替手動創建的臨時表**

union是一個臨時用多個table組成的查詢表，跟join的差異是union會顯示全部有重複的欄位資料，join只會顯示指定欄位資料

#### 使用index查詢

可以用查詢索引名稱，能快速找到資料

```sql
select id from user where (index(索引名稱))
```

參考資料：

- https://www.cloudways.com/blog/mysql-performance-tuning/
- https://codingnote.cc/zh-tw/p/21053/
- http://inspiregate.com/programming/mysql/360-8-strokes-allows-you-to-optimize-query-performance-mysql.html
- https://www.ipshop.xyz/12094.html
- https://kknews.cc/zh-tw/code/vae3ppq.html