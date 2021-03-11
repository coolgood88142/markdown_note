---
title: "fugle"
date: "2021-02-04"
author: "fugle"
summary: "介紹使用中央氣象局API結合Line Bot作天氣預報"
---

## 中央氣象局 API

### 大綱

介紹

Line Bot

1. 建立Providers 
2. 建立API
3. 安裝ngrok

流程圖

功能介紹

1. 查詢
2. 縣市menu
3. 今天氣候
4. 明天氣候
5. 雷達

### 介紹

是中央氣象局提供的開放式資料，我們可以利用API快速拿到氣象局的最新資訊。

使用之前要先到[氣象資料開放平台](https://opendata.cwb.gov.tw/index)註冊帳號，登入之後點選取得授權碼

![fugle](C:\xampp\htdocs\markdown_note\assets\images\weather.PNG)

再到開放資料平台的API網站，用授權碼測試取得資料，成功之後，下方有可以把整個json資料下載下來看，以下示範API的寫法。

```
https://opendata.cwb.gov.tw/api/各種天氣資訊的url?Authorization=氣象api的token&locationName=縣市名稱
```

這裡有各種氣象資料，例如：未來兩天的天氣預報、目前的降雨量、風速、酸雨PH值、地震等等。

### Line Bot

Line Bot是Line 的聊天機器人，是一個單向傳輸文字、圖片等訊息，而且還免費，不會被依訊息則數來收費。它是一個官方帳號，加入好友後，用他接收你的服務發送過來的推播訊息。

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

選擇剛建立好的**Providers**，在點選**Messaging API**，到下面的**channel access token**，點選**lssue**，產生**token**，等等講解需要用到。

![line-bot7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot7.png>)

掃描**QRcode**之後，加入好友

![line-bot8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot8.png>)

#### 3.安裝ngrok

Line Bot需要用https協定可以測試，我們在本機開發，無法使用協定，因此我們需要安裝ngrok，幫我們產生https協定的網址。

下載完ngrok之後打開exe檔，我們用laravel 做本機開發，port會是8000，要輸入ngrok.exe http 8000，再複製https網址

**Line bot**需要**Webhook URL**才能連到部署的網址，需要靠**ngrok**建立臨時的部署網址

![line-bot9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot9.png>)

在回到Line Bot網頁上，裡面有個叫Webhook Settings，把https的網址貼上去後，點選Verify，在打開瀏覽器貼上https網址，這時ngrok.exe會多一行GET status200，代表成功了。

先到[ngrok](https://dashboard.ngrok.com/login)註冊帳號，註冊完之後下載檔案，在做解壓縮

![line-bot10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot10.png>)

![line-bot11](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot11.png>)

打開資料夾的ngrok.exe

![line-bot12](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot12.png>)

輸入本機網址port，ngrok.exe http 8000 ，**ngrok**會產生網址出來，將https的網址貼到**Line bot**的**Webhook URL**

![line-bot13](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot13.png>)

![line-bot14](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot14.png>)

### 流程圖

![weather-1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/weather-1.png>)

![weather-2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/weather-2.png>))



![weather-3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/weather-3.png>)

![weather-4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/weather-4.png>)

### 功能介紹

#### 共用的function

```php
//使用API拿到中央氣象局的各種氣候資料
public function getCrawlerData(Client $client, String $weather, String $locationName){
    $token = '中央氣象局的token';
    $weatherUrl = 'https://opendata.cwb.gov.tw/api';
    $url = $weatherUrl . $weather . '?Authorization=' . $token;
        
    if($locationName!=null && $locationName!=''){
        $url = $url . '&locationName=' . $locationName;
    }
        
    $response = $client->get($url);
    $weatherData = json_decode($response->getBody())->records;

    return $weatherData;
}

//發送今天或明天氣候，要組flex message格式，並且跳出訊息
public function sendMessageWeather(int $type, String $cityText){
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
            //以下為flex message格式
            array_push($carouselData, $value);
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
        ];

        $weatherData = $weatherController->getCrawlerData($client, $weathers[13], $locationName);
        if(isset($weatherData->locations[0])){
            $weatherForecast = $weatherData->locations[0]->location[0]->weatherElement[6]->time;
            foreach($weatherForecast as $forecast){
                //以下為flex message格式
                array_push($messageArray, $value);
                $count++;
            }
        }

        $rain = $probabilityNum != 0 && $count > 1 ? round($probabilityNum/$count) : $probabilityNum;
            
        $carouselContentsData = [
            'type' => 'flex',
            'altText' => '氣候',
            'contents' => [
                'type' => 'bubble',
                'size' => 'giga',
                'hero' => [
                    'type' => 'image',
                    'url' => $this->getProbabilityOfPrecipitationImage((string) $rain),
                    'size' => 'full',
                    'aspectRatio' => '20:13',
                ],
                'body' => [
                    'type' => 'box',
                    'layout' => 'vertical',
                    'contents' => $messageArray
                ]
            ]
        ];
    }

    if($cityText != null){
        return $carouselContentsData;
    }
}

//判斷降雨機率未達20%顯示晴天圖片，20%-50%之間顯示多雲圖片，50%以上顯示下雨圖片
public function getProbabilityOfPrecipitationImage(String $probability_of_precipitation){
    $rain = (int)$probability_of_precipitation;
    $image = 'https://i.imgur.com/C5CarmM.jpg';

    if($rain >= 50){
        $image = 'https://i.imgur.com/fzUnYi1.jpg';
    }else if($rain > 20 && $rain < 50){
        $image = 'https://i.imgur.com/WRsK9Dg.jpg';
    }

    return $image;
}
```

#### 1.查詢

在我們告訴Line Bot什麼時候要查天氣，我們要設定輸入【氣候】，要系統告訴我們有那些縣市可以選擇

```php
public function getMessageWeather(Request $request){
	$replyToken = $request->events[0]['replyToken'];
    $messageBuilder = null;
    $text = $request->events[0]['message']['text'];
    $cityData = Config::get('city');
    $len = mb_strlen($text, 'utf-8');
    $text = str_replace('台','臺',$text);
    
    if($text == '氣候'){
        $cityText = '請輸入要查詢的縣市：' . "\n";
        foreach($cityData as $city){
            $cityText = $cityText . $city . "\n";
        }
    
        $cityText = rtrim($cityText, "\n");
        $messageBuilder = new \LINE\LINEBot\MessageBuilder\TextMessageBuilder($cityText);
    }else{
        $messageBuilder = new \LINE\LINEBot\MessageBuilder\TextMessageBuilder('請輸入【氣候】');
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

這裡是先判斷使用者是否輸入【氣候】，如果是的話，顯示縣市資料的訊息，如果不是就顯示【請輸入氣候】。

#### 2.縣市menu

點擊menu上的線圖，是提供股票最新各項即時資訊

```php
public function getMessageWeather(Request $request){
	$replyToken = $request->events[0]['replyToken'];
    $messageBuilder = null;
    $text = $request->events[0]['message']['text'];
    $cityData = Config::get('city');
    $len = mb_strlen($text, 'utf-8');
    $text = str_replace('台','臺',$text);
    
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
                    //...flax message格式
                ]
            ],
        );
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

檢查使用者是否傳縣市名稱，在套用Line bot的flex message格式，系統會跳出訊息，顯示使用者輸入縣市的今天或明天的menu。

#### 3.今天氣候

點擊menu上的的今天，會顯示今天的每隔3小時的溫度與降雨機率資料

```php
public function getMessageWeather(Request $request){
    $replyToken = $request->events[0]['replyToken'];
    $messageBuilder = null;
    $text = $request->events[0]['message']['text'];
    $cityData = Config::get('city');
    $len = mb_strlen($text, 'utf-8');
    $text = str_replace('台','臺',$text);
    
    if(strpos($text,'今天氣候')){
    	$cityWeather = mb_substr($text , 0 , 3, 'utf-8');
    	if(in_array($cityWeather, $cityData)){
            $fix = $this->sendMessageWeather(0, $cityWeather);
        }
        $messageBuilder = new RawMessageBuilder($fix);
    }else{
        $messageBuilder = new \LINE\LINEBot\MessageBuilder\TextMessageBuilder('請輸入【氣候】');
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

透過menu傳過來的參數，先判斷是否傳今天氣候的訊息，在套用Line bot的flex message格式，系統會自動回覆【縣市名稱+今天氣候】在跳出訊息，顯示今天縣市的天氣資訊。

#### 4.明天氣候

點擊menu上的的當日資訊，是提供股票基本資訊

```php
public function getMessageWeather(Request $request){
    $replyToken = $request->events[0]['replyToken'];
    $messageBuilder = null;
    $text = $request->events[0]['message']['text'];
    $cityData = Config::get('city');
    $len = mb_strlen($text, 'utf-8');
    $text = str_replace('台','臺',$text);
    
    if($text == '明天氣候'){
        $cityWeather = mb_substr($text , 0 , 3, 'utf-8');
    	if(in_array($cityWeather, $cityData)){
            $fix = $this->sendMessageWeather(1, $cityWeather);
        }
        $messageBuilder = new RawMessageBuilder($fix);
     }else{
        $messageBuilder = new TextMessageBuilder('目前股票尚未開盤');
     }	
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

透過menu傳過來的參數，先判斷是否傳今天氣候的訊息，在套用Line bot的flex message格式，系統會自動回覆【縣市名稱+明天氣候】在跳出訊息，顯示今天縣市的天氣資訊。

#### 5.雷達

顯示氣候雷達圖

```php
public function getMessageStock(Request $request){
	$replyToken = $request->events[0]['replyToken'];
    $messageBuilder = null;
    $text = $request->events[0]['message']['text'];
    $cityData = Config::get('city');
    $len = mb_strlen($text, 'utf-8');
    $text = str_replace('台','臺',$text);
    
    if($text == '雷達'){
        $url = 'https://www.cwb.gov.tw';
        $radarUrl = $url . '/V8/C/W/OBS_Radar.html';
        $content = $this->crawlerService->getOriginalData($radarUrl);
        $image =  $content->filter('div > img')->first()->attr('src');
        $radarImage = $url . $image;
        
        $messageBuilder =  new RawMessageBuilder(
            [
                'type' => 'flex',
                'altText' => '氣象雷達圖',
                'contents' => [
                    'type'=> 'bubble',
                    'body'=> [
                        'type'=> 'box',
                        'layout'=> 'vertical',
                        'contents'=> [
                            [
                                'type'=> 'image',
                                'url'=> $radarImage,
                                'size'=> 'full',
                                'aspectMode'=> 'cover',
                                'aspectRatio'=> '1:1',
                                'gravity'=> 'center'
                            ],
                       //以下為flex message格式
                    ]
                ]
            ]
        );
    }else{
        $messageBuilder = new TextMessageBuilder('目前股票尚未開盤');
    }	
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}

//crawlerService
public function getOriginalData(string $path)
{
    $content = $this->client->get($path)->getBody()->getContents();
    $crawler = new Crawler();
    $crawler->addHtmlContent($content);

    return $crawler;
}
```

使用者輸入雷達時，系統使用`GuzzleHttp`做爬蟲，爬中央氣象局的雷達圖，在套用在套用Line bot的flex message格式，在跳出訊息，顯示氣候雷達圖。

### 問題紀錄

1. 使用ngrok在本機測試，系統一直顯示419 unknown status，用log找錯誤訊息也沒有顯示?

   要在app/http/Middleware/VerifyCsrfToken.php，這隻檔案的except新增`/webhook`，告訴laravel這個路徑不需要驗證。

   ```php
   protected $except = [
   	'/webhook'
   ];
   ```

2. Line Bot 組文字資料使用換行符號(/n)，怎麼沒反應

   在PHP已經都習慣寫單引號，要換改用雙引號才有用。

   

參考資料:

https://stackoverflow.com/questions/46266553/why-does-the-laravel-api-return-a-419-status-code-on-post-and-put-methods、

https://eric0324.github.io/2019/09/16/let-line-chatbot-say-hello-world/、

https://developers.line.biz/en/、

https://www.cwb.gov.tw/V8/C/、

https://opendata.cwb.gov.tw/dist/opendata-swagger.html、



