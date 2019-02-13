1. ### 學習了什麼技術，並應用在哪

- java:應用在寫計算

- SQL-server:用jdbc的connection物件與SQL-server連線並回傳後端

- JQuery:寫在前端頁面跑function在頁面上做變化

- Framework

- - ZK:寫zul檔組標籤節點顯示前端
  - spring:定義外部檔案路徑，

- EJB:

- jasperreport

- SVN

- ajax

1. ### 遇到的困難

- java:資料型態與物件類別
- JQuery:網頁互動在網頁功能做哪些事件
- ZK:宣告zul檔裡的標籤，定義好跑哪些class做什麼事情
- spring:定義好設定檔用MVC架構
- EJB:用介面與serlvet定義好呼叫class有哪些function可以使用
- jasperreport:用jaspersfot studio 設計報表格式，將設計好的報表用javaBean套用到報表裡。
- SVN:版本控制需要注意
- ajax:跑後端serlvet回傳JSON物件的處理

1. ### 如何解決

- java:研究怎麼自己定義的物件類別要用哪些變數要用什麼資料型態，並用界面的方式，做呼叫在已有的function拿到自己定義的物件類別取想要變數資料。

- JQuery:研究寫一個js檔案自己定義function在jsp檔link js檔案並以用裡面的function跑事件以及用jquery的API跑想要的事件。

- ZK:建立zul檔用的java檔裡定義標籤跑類別做什麼事或者去別的類別取得變數資料，去實作zul檔。

- spring:研究建立設定檔(xml)bean的節點裡呼叫Controller，在Controller檔案建立類別寫jsp路徑，引用哪個java檔當後端跑class回傳物件到jsp裡去定義物件要做什麼。

- EJB:建立A類別寫function回傳物件， 寫一個介面實作A類別裡的function做回傳物件，然後介面implements A類別，在任一個類別裡直接用A類別時可實作類別的function拿到物件(前提是要先import A類別的變數)。

- jasperreport:寫一個類別從前端取得資料到這裡，用變數定義資料組SQL語法，用JDBC的connection回傳資料後，在寫一個session bean類別當欄位，把資料setbean裡用迴圈的方式組在list裡，在set到jasperreport放在dataSource在定義parameter參數寫報表名稱，在寫印置報表網格(jasper)檔案路徑跑jasperreport裡的印出報表的function。

- SVN:要先update最新的版本之後使用比對工具，比對最新版本與我的版本的差異，在改寫或覆蓋之前，先檢查整體程式流程以及編譯，改完之後在commit 打上message，在push上去。

- ajax:先定義Controller路徑以及傳參數的data，在Controller路徑定義的java類別回傳JSONObject物件到ajax的function去做解析拿到物件或資料。