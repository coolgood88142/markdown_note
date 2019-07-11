---
title: "Session"
date: "2019-06-12"
author: "session"
summary: "說明登入流程使用session機制"
---

什麼是Session?是被廣泛使用的一種技術，大多數的 Web 開發者，肯定都能運用自如。Server 和 Client 不會一直保持連線狀態，也不會有雙方狀態的即時更新，所以，Server 並不知道 Client 的狀態(是否登入)。

### 架構

使用會員登入驗證帳號密碼來記錄Session資料。

![session](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/session.png)



以下使用會員登入說明Form Submit使用Session機制:

### 登入介面

輸入帳密進行判斷，沒註冊帳號時，點選`還沒註冊嗎?`，有的話登入會判斷帳密是否已註冊。$_GET['sig_suc']顯示帳密是否有打正確。

```
 <a href="./sign">還沒註冊嗎?</a>
    <form action="logcheck.php" method="post">
         <input type="text" name="account" value="" placeholder="輸入帳號">
         <input type="password" name="password" value="" placeholder="輸入密碼">
         <input type="submit" name="" value="登入">
    </form>
    <?php if ($_GET['sig_suc']!=''): ?>
        <?php echo $_GET['sig_suc'] ?>
    <?php endif; ?>
    <?php if ($_GET['error']!=''): ?>
         <?php echo $_GET['error'] ?>
    <?php endif; ?>
```

### 登入判斷

先從資料庫取得帳號密碼資訊是否正確，假設完全正確，就把資料存進SESSION並回傳登入成功，後來會改成登入後畫面

```
<?php
   $account = $_POST['account'];
   $password = $_POST['password'];
   $select = $connect -> prepare("SELECT account,password FORM member WHERE account = :acc AND password = :pw");
   $select -> execute(array(':acc' => $account,':pw' => $password));
   $result = $select -> fetch(PDO::FETCH_ASSOC) ;
      if ($result['account']==$account&&$result['password']==$password) {
           session_start();
           $_SESSION['member'] = $result;
           header("location:./?error=登入成功");
      }elseif ($result['password']!=$password||$result['account']!=$account) {
                  header("location:./?error=帳密錯誤");
      }elseif ($result['password']!=''||$result['account']!='') {
                  header("location:./?error=輸入不完全");
      }
      
 ?>
```

### 登入後頁面

$_SESSION['member']會隨著切換頁面存在，代表目前已登入的狀態

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>主頁</title>
  </head>
  <body>
    <a href="./login/">登入</a>
      <?php
        session_start();
        var_dump($_SESSION['member'])
       ?>
  </body>
</html>
```



## 問題

#### session存在哪?client怎麼取得

session存在於server端，會隨著切換頁面存在，client端有seesion ID存在cookie裡，當使用者要轉頁面時會跟server端要資料時，可由session的ID去跟取得資料，回傳到client端確認目前是否已登入。



#### 要如何識別已登入，例如:有個session id為member記錄已登入狀態

第一次登入時，Client端向Server端檢查帳號密碼是否正確，確定後在設定session id名為member為true，存在Server端，之後將member回傳給Client端記錄在cookie裡，當網頁轉頁時Client端將cookie裡member傳給Server端，確認session id名為member的值是什麼，在回傳給Client端，表示目前已登入狀態，達到網頁保持已登入。



實做:1.輸入帳號密碼頁面 2.驗證帳號密碼資料與記錄session id與cookie的檔案 3.當cookie回傳到client端時，因為有cookie記錄命名session id的資料，再次與server端建立對話用cookie記錄命名session id，取得目前是否已登入





參考資料:

<https://ithelp.ithome.com.tw/articles/10185311>、

<http://fred-zone.blogspot.com/2014/01/web-session.html>、

<https://progressbar.tw/posts/92>、[https://codertw.com/%E4%BC%BA%E6%9C%8D%E5%99%A8/153752/](https://codertw.com/伺服器/153752/)