---
title: "fugle API"
date: "2021-03-12"
author: "fugle"
summary: "介紹使用富果台股API結合Line Bot做股票資訊"
categories: ["API document"]
tags: ["fugle","fugle API","富果","富果台股 API"]
---

## 富果台股 API

### 大綱

介紹

- 富果API有些股票資訊?

Line Bot

1. 建立Providers 
2. 建立API
3. 安裝ngrok

流程圖

功能介紹

1. 查詢
2. 線圖
3. 統計資訊
4. 當日資料
5. 當日成交資訊

問題記錄

### 介紹

[富果 API ](https://developer.fugle.tw/)是由 [時報資訊](https://info.infotimes.com.tw/) 與 Fugle 富果技術團隊共同開發，提供台股及時行情 。

還需要申辦玉山證券富果帳戶，才可以免費申請  API token 使用 Fugle 富果 API。

在申辦玉山證券富果帳戶，必須要有玉山銀行帳戶，開戶還需要7天作業時間。

![fugle](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fugle.PNG>)

#### 富果API有哪些股票資訊?

API類型分為線圖(chart)、統計資訊(Quote)、當天資訊(Meta)、當天成交資訊(Deatlls)。

![fugle-1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fugle-1.PNG>)

在拿資料前要做驗證，確認使用者輸入的股票代號，以下示範API的寫法


```
https://api.fugle.tw/realtime/v0/intraday/類型?symbolId=股票代號&apiToken=富果帳號的token
```

例如回傳的當天資訊資料，在data的meta裡，會記錄著股票的資料，顯示股票名稱、做什麼產業等等

![fugle-2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/fugle-2.PNG>)

以下程式碼為範例，拿股票名稱，如果回傳的是null就是驗證失敗

```php
public function getStockName(String $fugleUrl, String $parameter, String $symbolId){
    $url = $fugleUrl . 'meta' . $parameter . '&symbolId='. (int)$symbolId;
    $Guzzleclient = new \GuzzleHttp\Client();
    $name = null;
    try{
        $response = $Guzzleclient->get($url);
        $json = json_decode($response->getBody());
        $name = $json->data->meta->nameZhTw;
    } catch (RequestException $e) {
        return $name;
    }
    return $name;
}
```

## Line Bot

Line Bot是Line 的聊天機器人，是一個單向傳輸文字、圖片等訊息，而且還免費，不會被依訊息則數來收費。它是一個官方帳號，加入好友後，用他接收你的服務發送過來的推播訊息。

### 1.建立Providers 

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

### 2.建立API

選擇剛建立好的**Providers**，在點選**Messaging API**，到下面的**channel access token(long-lived)**，點選**lssue**，產生**token**，等等講解需要用到。

![line-bot7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot7.png>)

掃描**QRcode**之後，加入好友

![line-bot8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/line-bot8.png>)

### 3.安裝ngrok

Line Bot需要用https協定可以測試，我們在本機開發，無法使用協定，因此我們需要安裝ngrok，幫我們產生https協定的網址。

下載完ngrok之後打開exe檔，我們用laravel 做本機開發，port會是8000，要輸入ngrok.exe http 8000，再複製https網址

**Line bot**需要**Webhook URL**才能連到部署的網址，但是現在要連線到本機，需要靠**ngrok**建立臨時的部署網址

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

## 流程圖

![stock-1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/stock-1.png>)

![stock-2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/stock-2.png>)



![stock-3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/stock-3.png>)

![stock-4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/stock-4.png>)

![stock-5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/stock-5.png>)

## 功能介紹

### 1.股票menu

在我們需要一個menu可以點選股票資訊，來回應各種資料，前提是使用者要告訴我們，你要查哪支股票資訊?

所以會請使用者輸入股票代號後，系統會提供該股票相關的menu，menu的格式可以用這個[網址](https://developers.line.biz/flex-simulator/)去設計

```php
public function getMessageStock(Request $request){
	$replyToken = $request->events[0]['replyToken'];
    $type = $request->events[0]['type'];
    $symbolId = '';
    
    //先判斷是否從Line裡輸入文字，如果是的話type會是message
    if($type == 'message'){
        //['message']['text']是指訊息文字
        $symbolId = $request->events[0]['message']['text'];
    }

    $apiToken = '富果帳號的token';
    $fugleUrl = 'https://api.fugle.tw/realtime/v0/intraday/';
    $parameter = '?apiToken=' . $apiToken;
    
    //先檢查輸入的股票代號，是否會取得股票名稱(ex:2330-台積電)
    $name = $this->getStockName($fugleUrl, $parameter, $symbolId);

    //預設回傳訊息為請輸入【股票代碼】
    $messageBuilder = new TextMessageBuilder('請輸入【股票代碼】');
    if($name != ''){
        $size = 'lg';
        if(strlen($name) > 2 ){
            $size = 'md';
        }
        
        //組回傳訊息的格式
        $messageBuilder =  new RawMessageBuilder(
            [
                'type' => 'flex',
                'altText' => '請問要選擇哪個' . $name .'股票資訊?',
                'contents' => [
                    'type'=> 'bubble',
                    'hero'=> [
                        'type'=> 'image',
                        'url'=> 'https://i.imgur.com/Yx8s4WL.png',
                        'size'=> 'full',
                        'aspectRatio'=> '20:13',
                        'aspectMode'=> 'cover'
                     ],
                     'body'=> [
                         'type'=> 'box',
                         'layout'=> 'vertical',
                         'contents'=> [
                             [
                                 'type'=> 'text',
                                 'text'=> '請問要選擇哪個' . $name . '股票資訊?',
                                 'weight'=> 'bold',
                                 'size'=> $size,
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
                                     'type'=>'postback',
                                     'label' => '線圖',
                                     'text' => $name . '-線圖',
                                     'data' => 'text=線圖&symbolId='.$symbolId,
                                 ],
                                 'height'=>'sm'
                             ],
                             //以下為flex message格式，其餘3個股票資訊格式...
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
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

我們先用api的meta，檢查股票代號，並且取得股票名稱，在建立menu的flex message的格式

### 2.線圖

點擊menu上的線圖，是提供股票最新各項即時資訊

```php
public function getMessageStock(Request $request){
    $text = '';
    $symbolId = '';
    $type = $request->events[0]['type'];
    $apiToken = '富果帳號的token';
    $fugleUrl = 'https://api.fugle.tw/realtime/v0/intraday/';
    $parameter = '?apiToken=' . $apiToken;
    
    //這裡會先判斷menu設定的type是否為postback，postback可以夾帶參數和設定點擊時會顯示什麼訊息
    if($type == 'postback'){
        //例如postback的data是長這樣，text=線圖&symbolId=2330
        $data = $request->events[0]['postback']['data'];
        $dataArray = explode('&', $data);
        $textArray = explode('=', $dataArray[0]);
        $symbolIdArray = explode('=', $dataArray[1]);
        $text = $textArray[1];
        $symbolId = $symbolIdArray[1];
    }
    
    if($text == '線圖'){
        $url = $fugleUrl . 'chart' . $parameter;
        $Guzzleclient = new \GuzzleHttp\Client();
        $response = $Guzzleclient->get($url);
        $json = json_decode($response->getBody());
        $datas = $json->data->chart;
        $deatsKeys = array_keys(get_object_vars($datas));
    
        if(count($deatsKeys) > 0){
            $lastKey = $deatsKeys[count($deatsKeys) - 1];
            $lastDeatlsData = $datas->$lastKey;
            $charts = Config::get('chart');
    
            $messageArray = [
                [
                 'type'=> 'text',
                 'text'=> $name . '股票',
                 'weight'=> 'bold',
                 'size'=> 'xxl',
                 'margin'=> 'md'
                ],
                [
                    'type'=> 'separator',
                    'margin'=> 'xxl'
                ],
            ];
    
            foreach($charts as $chart){
                foreach($chart as $key => $value){
                    $chartValue = $lastDeatlsData->$key;
                    if($key != 'unit' && $key != 'volume'){
                        $chartValue = '$' . $chartValue;
                    }
                    
                    $message = $this->getStockHorizontalTemplate((string)$value, (string)$chartValue);
                    
                    array_push($messageArray, $message);
                    
                    if(count($messageArray) == 1){
                        array_push($messageArray[1], [
                            'margin'=> 'xxl',
                            'spacing'=> 'sm',
                        ]);
                    }
                }
            }
            
            $messageBuilder =  new RawMessageBuilder(
                [
                    'type' => 'flex',
                    'altText' => $name . '股票',
                    'contents' => [
                        'type'=> 'bubble',
                        'size'=> 'mega',
                        'body'=> [
                            'type'=> 'box',
                            'layout'=> 'vertical',
                            'contents'=> $messageArray
                        ]  
                    ]
                ]
            );
        }else{
            $messageBuilder = new TextMessageBuilder('目前股票尚未開盤');
        }
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

透過menu傳過來的參數，先判斷目前是點哪個資訊，直接取得股票資料，在套用Line bot的flex message格式，系統會自動回覆【股票名稱-線圖】，在跳出訊息，顯示股票的開盤價、最高價、最低價、收盤價、交易張數、交易量。

### 3.統計資訊

點擊menu上的的統計資訊，是提供股票每筆交易金額、狀態、最佳五檔及其他資訊

```php
public function getMessageStock(Request $request){
    $text = '';
    $symbolId = '';
    $type = $request->events[0]['type'];
    $apiToken = '富果帳號的token';
    $fugleUrl = 'https://api.fugle.tw/realtime/v0/intraday/';
    $parameter = '?apiToken=' . $apiToken;
    
    //這裡會先判斷menu設定的type是否為postback，postback可以夾帶參數和設定點擊時會顯示什麼訊息
    if($type == 'postback'){
        //例如postback的data是長這樣，text=統計資訊&symbolId=2330
        $data = $request->events[0]['postback']['data'];
        $dataArray = explode('&', $data);
        $textArray = explode('=', $dataArray[0]);
        $symbolIdArray = explode('=', $dataArray[1]);
        $text = $textArray[1];
        $symbolId = $symbolIdArray[1];
    }
    
    if($text == '統計資訊'){
    	$url = $fugleUrl . 'quote' . $parameter;
        $Guzzleclient = new \GuzzleHttp\Client();
        $response = $Guzzleclient->get($url);
        $json = json_decode($response->getBody());
        $datas = get_object_vars($json->data->quote);
    
        if(count($datas) > 0){
            $quotes = Config::get('quote');
            $messageArray1 = [
                [
                    'type'=> 'text',
                    'text'=> $name . '股票',
                    'weight'=> 'bold',
                    'size'=> 'xxl',
                    'margin'=> 'md'
                ],
                [
                    'type'=> 'separator',
                    'margin'=> 'xxl'
                ],
            ];
    
            $messageArray2 = [];
    
            foreach($quotes as $quote){
                foreach($quote as $key => $value){
                    //以下為flex message格式
                    array_push($messageArray2, $value);
                }
            }
            
            $messageBuilder =  new RawMessageBuilder(
                [
                    'type' => 'flex',
                    'altText' => $name . '股票',
                    'contents' => [
                        'type' => 'carousel',
                        'contents' => [
                            [
                                'type'=> 'bubble',
                                'size'=> 'giga',
                                'body'=> [
                                    'type'=> 'box',
                                    'layout'=> 'vertical',
                                    'contents'=> $messageArray1
                                ]
                            ],
                            [
                                'type'=> 'bubble',
                                'size'=> 'giga',
                                'body'=> [
                                    'type'=> 'box',
                                    'layout'=> 'vertical',
                                    'contents'=> $messageArray2
                                ]
                            ]
                        ]
                    ]
                ]
            );
        }else{
            $messageBuilder = new TextMessageBuilder('目前股票尚未開盤');
        } 
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

透過menu傳過來的參數，先判斷目前是點哪個資訊，直接取得股票資料，在套用Line bot的flex message格式，系統會自動回覆【股票名稱-統計資訊】，在跳出訊息，顯示當天的股票狀態、最後一筆的交易資訊、最新一筆最佳五檔資料。

五檔指的是目前成交價上下最接近的5個價位，分別有多少買家和賣家。

### 4.當日資訊

點擊menu上的的當日資訊，是提供股票基本資訊

```php
public function getMessageStock(Request $request){
    $text = '';
    $symbolId = '';
    $type = $request->events[0]['type'];
    $apiToken = '富果帳號的token';
    $fugleUrl = 'https://api.fugle.tw/realtime/v0/intraday/';
    $parameter = '?apiToken=' . $apiToken;
    
    //這裡會先判斷menu設定的type是否為postback，postback可以夾帶參數和設定點擊時會顯示什麼訊息
    if($type == 'postback'){
        //例如postback的data是長這樣，text=當日資訊&symbolId=2330
        $data = $request->events[0]['postback']['data'];
        $dataArray = explode('&', $data);
        $textArray = explode('=', $dataArray[0]);
        $symbolIdArray = explode('=', $dataArray[1]);
        $text = $textArray[1];
        $symbolId = $symbolIdArray[1];
    }
    
    if($text == '當日資訊'){
		$url = $fugleUrl . 'meta' . $parameter;
        $Guzzleclient = new \GuzzleHttp\Client();
        $response = $Guzzleclient->get($url);
        $json = json_decode($response->getBody());
        $datas = get_object_vars($json->data->meta);
    
        if(count($datas) > 0){
            $metas = Config::get('meta');
    
            $messageArray = [
                [
                    'type'=> 'text',
                    'text'=> $name . '股票',
                    'weight'=> 'bold',
                    'size'=> 'xxl',
                    'margin'=> 'md'
                ],
                [
                    'type'=> 'separator',
                    'margin'=> 'xxl'
                ],
            ];
        
            foreach($metas as $meta){
                foreach($meta as $key => $value){
                    //以下為flex message格式
                    array_push($messageArray, $value);
                }
            }
        
            $messageBuilder =  new RawMessageBuilder(
                [
                    'type' => 'flex',
                    'altText' => $name . '股票',
                    'contents' => [
                        'type'=> 'bubble',
                        'body'=> [
                            'type'=> 'box',
                            'layout'=> 'vertical',
                            'contents'=> $messageArray
                        ]  
                    ]
                          
                ]
            );
        }else{
            $messageBuilder = new TextMessageBuilder('目前股票尚未開盤');
        }	
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

透過menu傳過來的參數，先判斷目前是點哪個資訊，直接取得股票資料，在套用Line bot的flex message格式，系統會自動回覆【股票名稱-統計資訊】，在跳出訊息，顯示當天的股票名稱、產業、今日參考值、漲停價、跌停價等等資訊。

### 5.當日成交資訊

點擊menu上的的當日成交資訊，是提供股票當天最新一筆成交資訊

```php
public function getMessageStock(Request $request){
    $text = '';
    $symbolId = '';
    $type = $request->events[0]['type'];
    $apiToken = '富果帳號的token';
    $fugleUrl = 'https://api.fugle.tw/realtime/v0/intraday/';
    $parameter = '?apiToken=' . $apiToken;
    
    //這裡會先判斷menu設定的type是否為postback，postback可以夾帶參數和設定點擊時會顯示什麼訊息
    if($type == 'postback'){
        //例如postback的data是長這樣，text=當日成交資訊&symbolId=2330
        $data = $request->events[0]['postback']['data'];
        $dataArray = explode('&', $data);
        $textArray = explode('=', $dataArray[0]);
        $symbolIdArray = explode('=', $dataArray[1]);
        $text = $textArray[1];
        $symbolId = $symbolIdArray[1];
    }
    
    if($text == '當日成交資訊'){
		$url = $fugleUrl . 'dealts' . $parameter . '&limit=1';
        $Guzzleclient = new \GuzzleHttp\Client();
        $response = $Guzzleclient->get($url);
        $json = json_decode($response->getBody());
        $datas = $json->data->dealts;
                
        f(count($datas) > 0){
            $dealts = Config::get('dealts');
    
            $messageArray = [
                [
                    'type'=> 'text',
                    'text'=> $name . '股票',
                    'weight'=> 'bold',
                    'size'=> 'xxl',
                    'margin'=> 'md'
                ],
                [
                    'type'=> 'separator',
                    'margin'=> 'xxl'
                ],
            ];
    
            $dealt = end($dealts);
                    
            foreach($dealt as $key => $value){
                //以下為flex message格式
                array_push($messageArray, $message);
            }
        
            $messageBuilder =  new RawMessageBuilder(
                [
                    'type' => 'flex',
                    'altText' => $name . '股票',
                    'contents' => [
                        'type'=> 'bubble',
                        'body'=> [
                            'type'=> 'box',
                            'layout'=> 'vertical',
                            'contents'=> $messageArray
                        ]  
                    ]
                          
                ]
            );
        }else{
            $messageBuilder = new TextMessageBuilder('目前股票尚未開盤');
        }	
    }
    
    //這裡是告訴Line bot要顯示訊息版面的物件
    $response = $this->bot->replyMessage($replyToken, $messageBuilder);

    if ($response->isSucceeded()) {
        echo 'Succeeded!';
        return;
    }
}
```

透過menu傳過來的參數，先判斷目前是點哪個資訊，直接取得股票資料，在套用Line bot的flex message格式，系統會自動回覆【股票名稱-當日成交資訊】，在跳出訊息，顯示當天的股票最新交易時間、價格、張數、序號。



## 問題紀錄

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

https://developer.fugle.tw/document/intraday/introduction





