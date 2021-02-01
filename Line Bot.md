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

##### (一)使用者輸入【氣候】，顯示全台縣市資料

```php
private $bot;
private $channel_access_token;
private $channel_secret;

public function __construct()
{
    $this->lineBotService = app(LineBotService::class);
    $this->channel_access_token = env('LINE_BOT_CHANNEL_ACCESS_TOKEN');
    $this->channel_secret = env('LINE_BOT_CHANNEL_SECRET');
    $httpClient = new CurlHTTPClient($this->channel_access_token);
    $this->bot = new LINEBot($httpClient, ['channelSecret' => $this->channel_secret]);
}
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
         }
     }
    
     $response = $this->bot->replyMessage($replyToken, $messageBuilder);

	 if ($response->isSucceeded()) {
         echo 'Succeeded!';
         return;
     }
}

public function getCrawlerData(Client $client, String $weather, String $locationName)
{
    $token = '中央氣候局API的token';
    $weatherUrl = 'https://opendata.cwb.gov.tw/api';
    $url = $weatherUrl . $weather . '?Authorization=' . $token . '&locationName=' . $locationName;
    $response = $client->get($url);
    $weatherData = json_decode($response->getBody())->records;

    return $weatherData;
}
```

當使用者輸出【氣候】，系統會先判斷文字是否為氣候，如果不是系統回傳 `請輸入【氣候】`，如果是就跑foreach迴圈組訊息文字，之後將文字使用TextMessageBuilder物件，在用LINEBot物件replyMessage設定replyToken和文字，發送訊息

結果如下

```
//結果
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

##### (二)使用者輸入縣市名稱後，顯示詢問今天還是明天?

```php
private $bot;
private $channel_access_token;
private $channel_secret;

public function __construct()
{
    $this->lineBotService = app(LineBotService::class);
    $this->channel_access_token = env('LINE_BOT_CHANNEL_ACCESS_TOKEN');
    $this->channel_secret = env('LINE_BOT_CHANNEL_SECRET');
    $httpClient = new CurlHTTPClient($this->channel_access_token);
    $this->bot = new LINEBot($httpClient, ['channelSecret' => $this->channel_secret]);
}
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

        if(in_array($text, $cityData)){
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
    
     $response = $this->bot->replyMessage($replyToken, $messageBuilder);

	 if ($response->isSucceeded()) {
         echo 'Succeeded!';
         return;
     }
}

public function sendMessageWeather(int $type, String $cityText)
{
    $cityArray = [];
    $now = Carbon::now()->timezone('Asia/Taipei');
    $yesterday = $now->yesterday()->format('m/d');
    $today = $now->format('m/d');
    $tomorrow = $now->tomorrow('Asia/Taipei')->format('m/d');
    $carouselData = [];
    $carouselContentsData = [];

    if($type == 1){
        if($cityText != null){
            $cityArray = [$cityText];
        }

        $datas = DB::table('weather_tomorrow')->whereIn('city', $cityArray)->get();

        foreach($datas as $data){
            $city = $data->city;
            $temperature = $data->temperature;
            $probability_of_precipitation = $data->probability_of_precipitation;
            $temperature_text = $yesterday . ' 18:00 - '. $today .' 06:00';
            $carousel = [];

            $time_period = $data->time_period;
            $date = $today;
            $temperature_text = ' 06:00 - 18:00';
            if($time_period == 1){
                $temperature_text = ' 18:00 - 06:00';
                $date = $today . '-' . $tomorrow;
            }else if($time_period == 2){ 
                $date = $tomorrow;
            }

            $url = $this->getProbabilityOfPrecipitationImage($probability_of_precipitation);
            $carousel = $this->getCarouselArray($url, $date, $temperature_text, $temperature, $probability_of_precipitation);
            array_push($carouselData, $carousel);
        }


         $carouselContentsData = [
             'type' => 'flex',
             'altText' => '氣候',
             'contents' => [
                 'type' => 'carousel',
                 'contents' => $carouselData
              ]
         ];
    }else{
        $weatherController = app(WeatherController::class);
        $client = new \GuzzleHttp\Client();
        $weathers = Config::get('weather');
        $locationName = urlencode($cityText);
        $todayDate = $now->format('Y-m-d');
        $hour = (int)$now->format('H');
        $count = 0;
        $probabilityNum = 0;
        $messageArray = [
            [
                'type' => 'text',
                'text' => $now->format('m/d'),
                'weight' =>'bold',
                'size'=>'xl'
            ]
          

            $weatherData = $weatherController->getCrawlerData($client, $weathers[13], $locationName);
            if(isset($weatherData->locations[0])){
                $weatherForecast = $weatherData->locations[0]->location[0]->weatherElement[6]->time;
                foreach($weatherForecast as $forecast){
                    $startTime = mb_substr($forecast->startTime, 0, 10, "utf-8");
                    $time = mb_substr($forecast->startTime, 11, 5, "utf-8");
                    $nowHour = (int)mb_substr($time, 0, 2, "utf-8");
                    if($startTime == $todayDate){
                        $data = mb_split('。', $forecast->elementValue[0]->value);
                        $probabilityOfPrecipitation = mb_substr($data[1], 5, 2, "utf-8");
                        $probabilityOfPrecipitation = str_replace('%','',$probabilityOfPrecipitation);
                        $probabilityNum = $probabilityNum . (int)$probabilityOfPrecipitation;
                        $temperature = mb_substr($data[2], 4, 2, "utf-8");
                        $temperature = str_replace('度','',$temperature);

                        $timeWeatherData = [
                            'type' => 'text',
                            'size'=>'xl',
                            'weight' =>'bold',
                            'text' => $time . ' 🌡️' . $temperature . '° 💧' . $probabilityOfPrecipitation . '%',
                        ];

                        array_push($messageArray, $timeWeatherData);
                        $count++;
                    }
                }
            }

            $rain = $probabilityNum != 0 && $count > 1 ? round($probabilityNum/$count) : $probabilityNum;
            
            $carouselContentsData = [
                'type' => 'flex',
                'altText' => '氣候',
                'contents' => [
                    "type"=> "bubble",
                    "size"=> "giga",
                    "hero"=> [
                        "type"=> "image",
                        "url"=> $this->getProbabilityOfPrecipitationImage((string) $rain),
                        "size"=> "full",
                        "aspectRatio"=> "20:13",
                    ],
                    "body"=> [
                        "type"=> "box",
                        "layout"=> "vertical",
                        "contents"=> $messageArray
                    ]
                ]
            ];
        }

        if($cityText != null){
            return $carouselContentsData;
        }
    }}
```

使用者輸入縣市，系統會先判斷文字是否為縣市名稱，如果不是系統回傳 `請輸入【氣候】`，如果是會組flex message的格式，在用RawMessageBuilder建立訊息視窗。

使用者點選今天，就會回傳`縣市`今天氣候，點選明天，就會回傳`縣市`今天氣候。

ex:使用者輸入臺北市，點選今天，回傳臺北市今天氣候，明天就傳臺北市明天氣候。

當點選今天或明天氣候，系統根據這兩個，在回傳臺北市現在氣候資料或明天氣候資料，

以下為實作截圖

![line-bot18](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot18.png>)

![line-bot19](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot19.png>)

![line-bot20](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot20.png>)

![line-bot21](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot21.png>)

##### (三)使用rich menu建立選單

進入LINE Official Account Manager，點選帳號後，在點選圖文選單

![line-bot26](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot26.png>)

輸入標題、使用期間

![line-bot27](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot27.png>)

在選擇版型，選好之後上傳想要的圖片

![line-bot28](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot28.png>)

![line-bot29](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot29.png>)

在設定每個圖片要點選後要做什麼事情，例如：連結

![line-bot30](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot30.png>)

以下為實作截圖

![line-bot22](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot22.png>)

![line-bot23](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot23.png>)

![line-bot24](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot24.png>)

![line-bot25](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot25.png>)

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



