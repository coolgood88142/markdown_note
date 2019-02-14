ajax error(xhr, status, error)

用登入頁面做範例

```

login.jsp

<html>

<head></head>

<script src="jquery-3.3.1.js"></script>

<?php include("test_score.php");?>

 <body>

   <table id="example">
   	<tr>
   		<td>學號</td>
   		<td>姓名</td>
   		<td>成績</td>
        </tr>
        <?php foreach($row as $key => $value){?>
        <tr>
        <td class="ts_no"><?=$value['ts_no']?></td>
        <td><input type="text" name="ts_name[]" value="<?=$value['ts_name']?>"/></td>
		<td><input type="text" name="ts_grade[]" value="<?=$value['ts_grade']?>" size="3"/></td>
   </tr>
   	<?php }?>

   </table>

   <input type="button" name="ts_update" value="更新" onclick="update()"/>

 </body>

 <script language="JavaScript">

   function update(){

​     var ts_no="",ts_name="",ts_grade="";

​    

​     $('#example .ts_no').each(function(i) {

​       ts_no = ts_no + $(this).text() + ",";

​     });

​     $("input[name='ts_name[]']").each(function(i) {

​       ts_name = ts_name + $(this).val() + ",";

​     });

​     $("input[name='ts_grade[]']").each(function(i) {

​       ts_grade = ts_grade + $(this).val() + ",";

​     });

​     ts_no = ts_no.substring(0, ts_no.length-1);

​     ts_name = ts_name.substring(0, ts_name.length-1);

​     ts_grade = ts_grade.substring(0, ts_grade.length-1);

​     var data = {

​         'isStatus':'update',

​         'ts_no': ts_no,

​         'ts_name': ts_name,

​         'ts_grade': ts_grade,

​       };

​     $.ajax({

​       url: "test_score.php",

​       type: "POST",

​       async: true,

​       dataType: "json",

​       data: data,

​       success: function(msg){

​         if(msg.success==true){

​           alert("修改成功!")

​         }

​       },

​       error:function(xhr, status, error){

​         alert(xhr.responseText);

​       }

​     });

   }

 </script>

</html>
```

```
mysql.php

<?php

   //建立mysql連線

   $servername = "localhost";

   $username = "root";

   $password = "1234";

   $dbname= "plan";

 try {

​       $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password,

​       array(PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES utf8"));

​       $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

   }catch(PDOException $e){

​       echo $e->getMessage();

   }

?>

test_score.php

<?php

   include("mysql.php");

   $ts_no="";$ts_name="";$ts_grade="";$success=false;

   if(isset($_POST['isStatus']) && $_POST['isStatus']=='update'){

​       $ts_no =  $_POST['ts_no'];

​       $ts_no = explode(",", $ts_no);

​       $ts_name =  $_POST['ts_name'];

​       $ts_name = explode(",", $ts_name);

​       $ts_grade =  $_POST['ts_grade'];

​       $ts_grade = explode(",", $ts_grade);

​       if(count($ts_no)>0){

​           for($i=0 ; $i<count($ts_no) ; $i++) {

​               $no = $ts_no[$i];

​               $name = $ts_name[$i];

​               $grade = $ts_grade[$i];

​               $sql = "UPDATE test_score SET ts_name = '$name' AND ts_grade = $grade WHERE ts_no = '$ts_no'";

​               $conn->exec($sql);

​               $success = true;

​           }

​       }

​       if($success==true){

​           $array = array('success' => true);

​           echo json_encode($array);

​       }

   }else{

​       $sql = "SELECT * FROM test_score";

​       $query = $conn->query($sql);

​       $row = $query->fetchAll(PDO::FETCH_ASSOC);

   }

?>
```

以上面SQL錯誤回傳說明

1.xhr：XMLHttpReques[t](http://www.w3.org/TR/XMLHttpRequest/)物件

- [s](http://www.w3.org/TR/XMLHttpRequest/#the-status-attribute)tatus：回傳[HTTP](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) response.status代碼
- [s](http://www.w3.org/TR/XMLHttpRequest/#the-status-attribute)tatusText：回傳[HTTP](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) response.status代碼訊息
- readyState：XMLHttpReques[t](http://www.w3.org/TR/XMLHttpRequest/).readyState目前客戶端的狀態
- responseText：回傳XMLHttpReques[t](http://www.w3.org/TR/XMLHttpRequest/)內容
- responseXML：回傳XMLHttpReques[t](http://www.w3.org/TR/XMLHttpRequest/)內容XML格式

例如:SQL語法錯誤，因為WHERE ts_no = '$ts_no' 這句，$ts_no是陣列資料型態導致拋錯

status：回傳代碼為200

statusText：對應代碼訊息為OK，代表有傳到後端

readyState：4，代表客戶端已完成。

responseText：後段程式碼錯誤訊息。

responseXML：後段程式碼錯誤訊息用標籤方式呈現。

"<br />↵<font size='1'><table class='xdebug-error xe-notice' dir='ltr' border='1' cellspacing='0' cellpadding='1'>↵<tr><th align='left' bgcolor='#f57900' colspan="5"><span style='background-color: #cc0000; color: #fce94f; font-size: x-large;'>( ! )</span> Notice: Array to string conversion in C:\xampp\htdocs\active_plan\testfile\test_score.php on line <i>21</i>.."

用正規畫檢化這段錯誤訊息(ajax responsetext error)

這種情況需要檢查後端回傳JSON物件內容

2.status：string

回傳服務器訊息timeout逾時、error錯誤、abort中止、parseerror分析錯誤、null(回傳訊息錯誤產生會null)

timeout：在ajax裡有設定timeout多少秒，限定多少秒之內回傳JSON物件，超過就顯示訊息。

error：代表ajax在後端發生錯誤。

abort：在定義ajax物件之前有先定義response.abort() funcrion，當發生錯誤時會請求中止未結束的ajax。

定義變數ajax function 傳後端未回傳時，若上一次的ajax請求未完成會中止目前的傳後端的動作

parseerror：將參數回傳後端做運算時解析錯誤，如上面程式碼在後段SQL錯誤無法解析；回傳後端運作時哪個資料型態變數錯誤

null：回傳資料為null，所以顯示null

3.error:string

回傳HTTP的代碼訊息，例如:404Not Found(錯誤的請求)、505Internal Server Error(內部服務器錯誤)、500、504、403..

400 Bad Request(請求無效) 

例如：程式語法錯誤、參數資料型態有問題

404 Not Found(錯誤的請求) 

例如：url後端檔案寫錯或表單內容寫錯

403 Forbidden(沒有權限，被禁止的) 

例如：使用的IP沒有允許權限或者沒有連線到內部網路

500 Internal Server Error(內部服務器錯誤) 

例如：請求server回傳資料找不到或網站升級和網站遷移

503  Service unavailable(後端錯誤請求)

例如：在後端執行時錯誤，回傳沒資料

504 Gateway Timeout(網頁請求超時) 

例如：程式執行時間過長逾時或者太多人使用同一個資料庫排隊到逾時

補上資料來源：網址