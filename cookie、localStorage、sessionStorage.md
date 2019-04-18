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
| 由瀏覽器產生cookie，需要關閉瀏覽器或生命週期過了才會被清除 | 除非被清除，否則永久保存  | 僅在當前畫面才有生效，關閉頁面或瀏覽器就會被清除 | 生命週期                                |
| 4K左右                  | 一般都是5MB               	| 一般都是5MB |大小|
| 由server請求傳遞，每次用http網頁，因為cookie資料過多帶來性能問題 | 只存在瀏覽器，不與server請求傳遞 | 只存在瀏覽器，不與server請求傳遞 | 與server傳遞資料                        |