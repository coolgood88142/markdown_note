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
public function saveWeatherApiData(){
    //先移除現有氣候資料
    $this->deleteWeatherData();
    $client = new \GuzzleHttp\Client();
        
    $weatherUrl = 'https://opendata.cwb.gov.tw/api';
    $cityData = Config::get('city');
    $weathers = Config::get('weather');
    $automatic = Config::get('automatic');

    $dataArray = [];
    $startTime = 6;
    $endTime = 18;
    $today = Carbon::now()->timezone('Asia/Taipei');
    $hour = (int)$today->format('H');
    $type = 0;

    if($hour > $endTime){
        $type = 2;
    }else if($hour <= $startTime){
        $type = 1;
    }

    foreach($cityData as $city){
        $weatherArray = [];
        $acidRainPh = 0;
        $locationName = urlencode($city);
        $temperature = '';
        $probabilityOfPrecipitation = '';

        $weatherForecastData = $this->getCrawlerData($client, $weathers[0], $locationName);
        if(isset($weatherForecastData->location[0])){
            $mint = $weatherForecastData->location[0]->weatherElement[2]->time[$type]->parameter->parameterName;
            $maxt = $weatherForecastData->location[0]->weatherElement[4]->time[$type]->parameter->parameterName;
            $temperature = $mint . '°-' . $maxt . '°';
            $probabilityOfPrecipitation = $weatherForecastData->location[0]->weatherElement[1]->time[$type]->parameter->parameterName;
        }

        $nextWeekWeather = '';
        $townshipWeatherForecastData = $this->getCrawlerData($client, $weathers[1], $locationName);
        if(isset($townshipWeatherForecastData->locations[0])){
            $nextWeekWeather = $townshipWeatherForecastData->locations[0]->location[0]->weatherElement[10]->time[$type]->elementValue[0]->value;
        }

        $windDirection = '';
        $anemometer = '';
        $relativeHumidity = '';
        $barometricPressure = '';
        $automaticWeatherData = $this->getCrawlerData($client, $weathers[2], urlencode($automatic[$city][0]));
        if(isset($automaticWeatherData->location[0])){
            $windDirection = $automaticWeatherData->location[0]->weatherElement[1]->elementValue;
            $anemometer = $automaticWeatherData->location[0]->weatherElement[2]->elementValue;
            $relativeHumidity = $automaticWeatherData->location[0]->weatherElement[4]->elementValue;
            $barometricPressure = $automaticWeatherData->location[0]->weatherElement[5]->elementValue;
        }
            

        $acidRainPh = 0;
        $acidRainData = $this->getCrawlerData($client, $weathers[5], $locationName);
        $rainPh = $acidRainData->weatherElement[0]->location;
        if(count($rainPh) > 0){
            $acidRainPh = $rainPh.value;
        }

        $ultravioletRaysData = $this->getCrawlerData($client, $weathers[6], $locationName);
        $ultravioletIndex = (String)$ultravioletRaysData->weatherElement->location[0]->value;

        $ozoneYearAvgData = $this->getCrawlerData($client, $weathers[7], $locationName);
        $ozoneYearAvg = $ozoneYearAvgData->location->weatherElement[0]->time[29]->elementValue;

        $seismicityData = $this->getCrawlerData($client, $weathers[8], $locationName);
        $seismicity = $seismicityData->earthquake[0]->reportContent;

        $smallAreaSeismicityData = $this->getCrawlerData($client, $weathers[9], $locationName);
        $smallAreaSeismicity = $smallAreaSeismicityData->earthquake[0]->reportContent;

        $sunriseData = $this->getCrawlerData($client, $weathers[11], $locationName);
        $sunrise = $sunriseData->note;

        $moonriseData = $this->getCrawlerData($client, $weathers[12], $locationName);
        $moonrise = $moonriseData->note;

        $data = [
            'city' => $city, 'temperature' => $temperature, 
            'probability_of_precipitation' => $probabilityOfPrecipitation, 'wind_direction' =>$windDirection,
            'anemometer' => $anemometer, 'barometric_pressure' => $barometricPressure,
            'relative_humidity' => $relativeHumidity, 'ultraviolet_index' => $ultravioletIndex,
            'seismicity' => $seismicity, 'small_area_seismicity' => $smallAreaSeismicity,
            'acid_rain_ph' => $acidRainPh, 'sunrise' => $sunrise, 'moonrise' => $moonrise,
            'ozone_year_avg' => $ozoneYearAvg, 'next_week_weather' => $nextWeekWeather,
            'created_at' => Carbon::now()->timezone('Asia/Taipei')
        ];
        array_push($dataArray, $data);
      }

      try {
         $db = DB::table('weather_info')->insert($dataArray);

      } catch (Exception $e) {
         $status = 'error';
         $message = '新增失敗!';
         dd($e);
      }
        
 }
```

使用輸入【氣候】，顯示全台縣市

```php
public function getMessageWeather(Request $request)
    {
        $replyToken = $request->events[0]['replyToken'];
        $messageBuilder = new \LINE\LINEBot\MessageBuilder\TextMessageBuilder('請輸入【氣候】');
        if(isset($request->events[0]['postback'])){
            $messageBuilder =  new \LINE\LINEBot\MessageBuilder\TextMessageBuilder($request->events[0]['postback']['data']);
        }else{
            $text = $request->events[0]['message']['text'];
            $cityData = Config::get('city');
            $len = mb_strlen($text, 'utf-8');
            $text = str_replace('台','臺',$text);
            $messageBuilder = '';

            if($text == '氣候'){
                $cityText = '請輸入要查詢的縣市：' . "\n";
                foreach($cityData as $city){
                    $cityText = $cityText . $city . "\n";
                }
    
                $cityText = rtrim($cityText, "\n");
                $messageBuilder = new \LINE\LINEBot\MessageBuilder\TextMessageBuilder($cityText);
            }else if(in_array($text, $cityData)){
                // $fix1 = $this->sendMessageWeather(0, $text);
                // $fix2 = $this->sendMessageWeather(1, $text);

                $messageBuilder =  new RawMessageBuilder(
                    [
                        'type' => 'flex',
                        'altText' => '請問要選擇哪一天?',
                        'contents' => [
                            'type'=> 'bubble',
                            'hero'=> [
                                'type'=> 'image',
                                'url'=> 'https://i.imgur.com/l8yNat5.jpg',
                                'size'=> 'full',
                                'aspectRatio'=> '20:13',
                                'aspectMode'=> 'cover'
                            ],
                            'body'=> [
                                'type'=> 'box',
                                'layout'=> 'vertical',
                                'contents'=> [
                                    [
                                        'type'=>'text',
                                        'text'=>'請問要選擇哪一天?',
                                        'weight'=> 'bold',
                                        'size'=> 'xl',
                                        'margin'=>'md'
                                    ],
                                    [
                                        'type'=>'spacer'
                                    ],
                                ],
                            ],
                            'footer'=> [
                                'type'=>'box',
                                'layout'=>'vertical',
                                'spacing'=>'sm',
                                'contents'=> [
                                    [
                                        'type'=>'button',
                                        'action'=>[
                                            'type'=>'message',
                                            'label' => '今天',
                                            'text'=> $text . '今天氣候',
                                        ],
                                        'height'=>'sm'
                                    ],
                                    [
                                        'type'=>'button',
                                        'action'=>[
                                            'type'=>'message',
                                            'label' => '明天',
                                            'text'=> $text . '明天氣候',
                                        ],
                                        'height'=>'sm'
                                    ]
                                ],
                                'flex'=> 0,
                            ],
                            'styles'=>[
                                'footer'=>[
                                    'separator'=> true
                                ],
                            ],
                        ]
                    ],
                );
                Log::info('組好了');
            }else if(strpos($text,'今天氣候') || strpos($text,'明天氣候')){
                $cityWeather = mb_substr($text , 0 , 3, 'utf-8');
                if(in_array($cityWeather, $cityData)){
                    $fix = '';
                    if(strpos($text,'今天氣候')){
                        $fix = $this->sendMessageWeather(0, $cityWeather);
                    }else if(strpos($text,'明天氣候')){
                        $fix = $this->sendMessageWeather(1, $cityWeather);
                    }
                }

                $messageBuilder = new RawMessageBuilder($fix);
            }
        }
       
        Log::info('發送前');
        // $richMenuBuilder = new \LINE\LINEBot\RichMenuBuilder();
        // $response = $this->$bot->createRichMenu($richMenuBuilder);
        // $response = $this->bot->getRichMenuList();
        $response = $this->bot->replyMessage($replyToken, $messageBuilder);
        


        if ($response->isSucceeded()) {
            echo 'Succeeded!';
            return;
        }
    }
```



```
請輸入要查詢的縣市：
基隆市
臺北市
新北市
桃園市
新竹市
新竹縣
苗栗縣
臺中市
彰化縣
南投縣
雲林縣
嘉義市
嘉義縣
臺南市
高雄市
屏東縣
臺東縣
花蓮縣
宜蘭縣
澎湖縣
金門縣
連江縣
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



