## **cookie、localStorage、sessionStorage差異**



#### cookie

生命週期為只在設定的cookie過期時間之前一直有效，即使視窗或瀏覽器關閉。 存放資料大小為4K左右 ，所以cookie只適合儲存很小的資料。



#### localStorage

localStorage生命週期是永久，除非使用者顯示在瀏覽器達到清除localStorage資訊，否則這些資訊將永遠存在。



#### sessionStorage

sessionStorage僅在當前畫面下有效，關閉頁面或瀏覽器後被清除。



不同瀏覽器無法共享localStorage或sessionStorage中的資訊。相同瀏覽器用不同頁面間可使用相同的 localStorage，但是sessionStorage不行。



**cookie、localStorage、sessionStorage表格差異**

| cookie          | localStorage         | sessionStorage   | 功能描述                                |
| ----------------------- | ------------------------- | ------------------------------------------------ | --------------------------------------- |
| 由瀏覽器產生cookie，需要關閉瀏覽器或生命週期過了才會被清除 | 除非被清除，否則無時間限制 | 記錄只存在目前頁面， | 生命週期-設定時間                           |
| V | 無法透過瀏覽器的輔助功能清除 | 無法透過瀏覽器的輔助功能清除 | 生命週期-使用者使用自行清除 |
| V | V                                      | V | 生命週期-網頁UI清除動作 |
| 只會在有設定瀏覽器中存在，直接時間結束 | 除非關閉原本開的瀏覽器，否則無時間限制 | 除非關閉原本開的瀏覽器，否則無時間限制 | 生命週期-換瀏覽器使用 |
| cookie只會在http網頁存取，存取一個變數為4KB | 存取一個變數為5MB         | 存取一個變數為5MB |大小|
| 每次用http網頁，因為cookie資料過多會占用頻寬導致網頁效能很慢 | 只存在瀏覽器，不占用頻寬也不會影響網頁效能 | 只存在瀏覽器，不占用頻寬也不會影響網頁效能 | 與server傳遞資料                        |



參考資料:[https://medium.com/@jscinin/javascript-cookie-localstorage-sessionstorage-%E4%B8%89%E7%A8%AE%E5%B7%AE%E7%95%B0-fe7f38260439](https://medium.com/@jscinin/javascript-cookie-localstorage-sessionstorage-三種差異-fe7f38260439)、<https://www.itread01.com/articles/1501797427.html>、<https://dotblogs.com.tw/shadow/2014/07/10/145879>