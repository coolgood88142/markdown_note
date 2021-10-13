## 如何處理php大數據分頁

### **php裡面上千萬的數據怎麼分頁**

我們可以用php+mySQL進行分頁，在web畫面上，

### **php，mysql怎麼對特別大的數據量進行分頁**

數據量很大的時候，一般都是用coun來統計總數據的，盡量不要使用select count() from table這種方式取得，執行時間會逾時。

首先確認總比數，然後確認每頁多少筆資料，再來要分了多少頁，最後在形成數個分頁。

###  **php 數據分頁顯示 多頁如何優化**

1. 減少對資料庫的讀取
2. 進行數據緩存
3. 利用資料庫的自身優化技術，例如索引
4. 精確查詢條件，有利於提高查找速度

### 範例

```php
$page=isset($GET['page'])?intval($_GET['page']):1; //拿到的分頁參數，來判斷顯示是第幾個分頁
$num=10; //每頁顯示10條數據

$db=mysql_connect("host","name","pass"); //創建資料庫連接
$select=mysql_select_db("db",$db); //選擇要操作的資料庫

$total=mysql_num_rows(mysql_query("select id from table")); //查詢數據的總數,id是資料庫中的一個自動賦值的欄位
$pagenum=ceil($total/$num); //獲得總頁數

//假如傳入的頁數參數大於總頁數，則顯示錯誤信息
if($page>$pagenum || $page == 0){
	Echo "Error : Can Not Found The page .";
	Exit;
}

For($i=1;$i<=$pagenum;$i++){

$show=($i!=$page)?"$i":"$i";
Echo $show." ";
}

/*顯示分頁信息
1 2 3 4 5 6
*/


```



