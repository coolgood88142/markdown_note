---

title: "Line Bot"
date: "2020-05-20"
author: "Line Bot"
summary: "介紹使用Line Bot"
---

## Line Bot

Line Bot是Line 的聊天機器人，是一個單向傳輸文字、圖片等訊息，而且還免費，不會被依訊息則數來收費。它是一個官方帳號，加入好友後，用他接收你的服務發送過來的推播訊息。

以下介紹使用器



#### 1.建立Providers 

先登入[Line ](https://developers.line.biz/en/)頁面，在**Providers**點選**Create**，輸入完**Provider name**之後

![line-bot1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot1.png>)

點選**Create a Messageing API channel**

![line-bot2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot2.png>)

輸入**Channel name**(頻道名稱)，在輸入**Channel description**，來描述頻道功能

![line-bot3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot3.png>)

再從**Category**選擇類別，再到**Subcategory**，選擇子類別

![line-bot4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot4.png>)

勾選下面的**LINE Official Account Terms of Use**和**LINE Official Account API Terms of Use**，在點選**Create**建立起來。

![line-bot5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot5.png>)

#### 2.建立API

選擇剛建立好的**Providers**，在點選**Messaging API**，到下面的**channel access token(long-lived)**，點選**lssue**，產生**token**，等等講解需要用到。

![line-bot7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot7.png>)

掃描**QRcode**之後，加入好友

![line-bot8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot8.png>)

#### 3.安裝ngrok

**Line bot**需要**Webhook URL**才能連到部署的網址，但是現在要連線到本機，需要靠**ngrok**建立臨時的部署網址

![line-bot9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot9.png>)

先到[ngrok](https://dashboard.ngrok.com/login)註冊帳號，註冊完之後下載檔案，在做解壓縮

![line-bot10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot10.png>)

![line-bot11](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot11.png>)

打開資料夾的ngrok.exe

![line-bot12](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot12.png>)

輸入本機網址port，ngrok.exe http 8000 ，**ngrok**會產生網址出來，將https的網址貼到**Line bot**的**Webhook URL**

![line-bot13](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot13.png>)

![line-bot14](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot14.png>)

#### 4.程式碼

Route

```
Route::post('/webhook','LineBotController@getMessageWeather');
```

執行明天臺北市、新北市的溫度、降雨機率，這裡直接拿DB，已經事先用排程新增DB資料

```php
public function sendMessageWeather(){
    $datas = DB::table('weather_tomorrow')->whereIn('city', ['臺北市', '新北市'])->get();
    $count = 0;
    $message = '';
    foreach($datas as $data){
        $city = $data->city;
        $time_period = $data->time_period;
        $temperature = $data->temperature;
        $probability_of_precipitation = $data->probability_of_precipitation;
        $temperature_text = '昨天18:00-今天06:00';
            
        if($time_period == 0){
            $message = $message . $city . ':' . "\n";
            }else if($time_period == 1){
                $temperature_text = '今天06:00-18:00';
            }else if($time_period == 2){
                $temperature_text = '今天18:00-明天06:00';
            }

            $message = $message . '【'. $temperature_text . ' 溫度為' . $temperature;
            $message = $message . '， 降雨機率為' . $probability_of_precipitation . '%】' . "\n";
        }

        $message = rtrim($message, "\n");

        $textMessageBuilder = new \LINE\LINEBot\MessageBuilder\TextMessageBuilder($message);
        $response = $this->sendMessage($textMessageBuilder);
	}
}
```

這裡抓DB資料，跑foreach迴圈組訊息文字，結果如下

```
臺北市:
【昨天18:00-今天06:00 溫度為16-17， 降雨機率為20%】
【今天06:00-18:00 溫度為16-19， 降雨機率為10%】
【今天18:00-明天06:00 溫度為15-17， 降雨機率為10%】
新北市:
【昨天18:00-今天06:00 溫度為16-18， 降雨機率為20%】
【今天06:00-18:00 溫度為16-20， 降雨機率為10%】
【今天18:00-明天06:00 溫度為15-17， 降雨機率為10%】
```

使用者輸入縣市，詢問縣市的氣候，是要今天還是明天

```php
public function getMessageWeather(Request $request)
    {
        $text = $request->events[0]['message']['text'];
        $replyToken = $request->events[0]['replyToken'];
        $cityData = Config::get('city');
        
        // $imageUrl = UrlBuilder::buildUrl($this->req, ['image', 'weather.jpg']);
        $messageBuilder = new \LINE\LINEBot\MessageBuilder\TextMessageBuilder('請輸入正確的縣市名稱');
        
        if(in_array($text, $cityData)){
            $messageBuilder = new \LINE\LINEBot\MessageBuilder\TemplateMessageBuilder(
                '詢問'. $text .'的氣候',
                new ConfirmTemplateBuilder('請問要選擇哪一天的氣候?', [
                    new MessageTemplateActionBuilder('今天', $text . '今天氣候'),
                    new MessageTemplateActionBuilder('明天', $text . '明天氣候'),
                ]));
        }
        
        $response = $this->bot->replyMessage($replyToken, $messageBuilder);

        if ($response->isSucceeded()) {
            echo 'Succeeded!';
            return;
        }
    }
```





#### 5.部署到heroku

註冊[HeroKu](<https://id.heroku.com/login>)帳號，在安裝[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#windows)，用cmd執行HeroKu

```bash
heroku
```

登入剛剛註冊的HeroKu帳號

```bash
heroku login
Email: coolgood88142@gmail.com
Password: xxxxxxx
Logged in as coolgood88142@gmail.com #登入成功
```

在heroku網站，點選create app

![line-bot15](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot15.png>)

複製heroku git url

![line-bot16](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot16.png>)

將Line Bot專案部署到heroku

```
git init
git remote add heroku https://git.heroku.com/fierce-headland-21046.git
git add .
git commit -m "my first line bot"
git push heroku master
```

上版之後，記得要將**Line bot**的**Webhook URL**換成**heroku**

![line-bot17](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot17.png>)

#### 問題紀錄：

1. 使用ngrok在本機測試，系統一直顯示419 unknown status，用log找錯誤訊息也沒有顯示?

   要在app/http/Middleware/VerifyCsrfToken.php，這隻檔案的except新增`/webhook`，告訴laravel這個路徑不需要驗證。

   ```php
   protected $except = [
   	'/webhook'
   ];
   ```

   這個問題卡了我兩天，老家在宜蘭，沒搞定都回不去了QQ

2. Line Bot 組文字資料使用換行符號(/n)，怎麼沒反應

   在PHP已經都習慣寫單引號，要換改用雙引號才有用。

參考資料:

https://stackoverflow.com/questions/46266553/why-does-the-laravel-api-return-a-419-status-code-on-post-and-put-methods、

https://eric0324.github.io/2019/09/16/let-line-chatbot-say-hello-world/、

https://developers.line.biz/en/



