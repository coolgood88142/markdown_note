---
title: "Line Notify"
date: "2020-05-20"
author: "Line Notify"
summary: "介紹使用Line Notify"
---

## Line Notify

Line Notify是Line 的推播通知服務，是一個單向傳輸文字、圖片等訊息，而且還免費，不會被依訊息則數來收費。它是一個官方帳號，加入好友後，用他接收你的服務發送過來的推播訊息。

以下介紹使用Google日曆行程結合Line Notify前10分鐘推播提醒通知



#### 1.建立Line Notify服務

先登入[Line Notif](https://notify-bot.line.me/zh_TW/)頁面，登入之後點選個人頁面，在點選發行權杖，輸入名稱後選擇**透過1對1聊天接收LINE Notify通知**，這時會產生一個金鑰，等等程式會用到，建立成功之後Line Notify官方帳號會加入好友。



#### 2.Google App Script專案

登入Google帳號後，在Google雲端硬碟的新增Google App Script專案，新增專案後補上程式碼，程式碼如下

ID在Google日曆的設定裡，點選**我的日曆的設定**，選擇要提醒的日曆，在整合日曆的日曆ID

程式碼.gs

```javascript
function getCalendar() {
  //以下說明日曆行程提醒的程式碼
  //透過ID取得日曆
  let cal = CalendarApp.getCalendarById("xxxxxx@gmail.com");
  let today = new Date();
  var todayHours = today.getHours();
  var todayMinutes = today.getMinutes() + 10;
  
  if (cal != null){
    //設定查詢日期區間
    let tenMinutes = new Date(today.getFullYear(), today.getMonth(), today.getDate(), todayHours, todayMinutes+1);
    let events = cal.getEvents(today, tenMinutes);
    let text = '';
    if(events.length > 0) {
      let data = '';
      
      events.forEach(function(el, index){
        let startTime = el.getStartTime();
        let hours = startTime.getHours();
        let minutes = startTime.getMinutes();
        
        //先判斷現在時間是否為行程時間的前10分鐘
        if(todayHours == hours && todayMinutes == minutes){
          data = el;
          return;
        }
      });
      
      if(data != ''){
        let title = data.getTitle();  //標題
        let description = data.getDescription();  //說明
        
        text = text + "\n標題：" + title + "\n描述：" + description;
        sendMessage(text);
      }
      Logger.log(text);
      
    } else {
      Logger.log("查詢的行程不存在！");
    }
  } else {
    Logger.log("日曆不存在！");
  }
}

function sendMessage(message) {
  //組好資料訊息的資料
  let token = "FboxNfk7e8WHQBj2pSqyL0tdMSFpAr6jC1e0vxTWYTM";

  //使用Line發送訊息
  let options =
   {
     "method"  : "post",
     "payload" : {"message" : message},
     "headers" : {"Authorization" : "Bearer " + token}
   };
   UrlFetchApp.fetch("https://notify-api.line.me/api/notify", options);

}

```

用getCalendar function取得現在時間後10分鐘，判斷現在是否為日曆行程前10分鐘，是的話執行sendMessage function 組提醒訊息後發送。

要在專案的啟動程序設定每分鐘執行getCalendar function，只有在日曆上有設定行程，系統會在前10分鐘發送訊息。





參考資料:

https://chibupapa.com/2019/11/12/line-notify/、

https://notify-bot.line.me/zh_TW/



