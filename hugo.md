## Hugo：部落格網站

什麼是Hugo?是一款static site generator，讓你可以專注撰寫內容，然後自動compile成網站。



以下分為兩部分介紹：本地建立檔案、部署到 GitHub。

## **本地建立檔案**

插入圖片說明怎麼操作，可減少文字敘述

1.先安裝hugo
2.新增一個資料夾名為hugo_blog，在進到hugo_blog資料夾，用cmd執行hugo，系統會自動產生資料夾
3.在新增一個資料夾名為public，當作帳號進入部落格，所以這個資料要做連結。
4.在github建立兩個repositories，一個取hugo_blog，一個取<帳號>.github.io

5.在hugo_blog資料夾，部落格主題使用hamburg為範例，用cmd執行 git submodule add https://github.com/hauke96/hugo-theme-hamburg.git themes/hamburg

6.這時系統會將hugo_blog/themes，產生資料夾名為hamburg，使用別人做好的主題用這個來連結

7.在hugo_blog/content 放入自己的markdown文件
8.在剛剛hugo_blog資料夾，使用cmd執行 git submodule add https://github.com/coolgood88142/coolgood88142.github.io.git public
9.這時系統會產生public資料夾，裡面會有剛剛建立帳號的repositories

10.用cmd執行 hugo server ，在本機網址輸入【localhost:1313】，看到自己建立部落格網頁



## **部署到 GitHub**

1.在刪除public資料夾後，請先執行git init 初始化後，在執行git submodule add -f https://github.com/coolgood88142/coolgood88142.github.io.git public

2.這時資料夾public是建立coolgood88142.github.io專案的連結

3.在hugo_blog資料夾直接執行上版

4.上版後在網只輸入coolgood88142.github.io(帳號)，就可以顯示個人部落格架設的網址





## 







參考資料:[https://medium.com/@chs_wei/%E5%9C%A8-github-%E9%83%A8%E7%BD%B2-hugo-%E9%9D%9C%E6%85%8B%E7%B6%B2%E7%AB%99-9c40682dfe40](https://medium.com/@chs_wei/在-github-部署-hugo-靜態網站-9c40682dfe40)