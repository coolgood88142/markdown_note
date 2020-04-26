## vue在程式遇到的問題?



#### component使用props的資料，無法變更

原因是全域變數，在instance是用同一個變數，不能隨意變更資料



#### computed使用data的資料卻沒有值，

去查生命週期怎麼執行computed的情況，為什麼沒有值?