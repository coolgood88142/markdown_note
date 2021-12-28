## FinMind API

### 什麼是FinMand

超過 50 種金融開源數據( open data )，希望讓大數據、資料分析，減少資料收集的門檻。

- 技術面 : 台股股價 daily、歷史 tick 逐筆交易、PER、PBR、每5秒委託成交統計、台股加權指數。
- 基本面 : 綜合損益表、現金流量表、資產負債表、股利政策表、除權除息結果表、月營收。
- 籌碼面 : 外資持股、股權分散表、融資融券、三大法人買賣、借券成交明細。
- 消息面 : 台股相關新聞。
- 衍生性商品 : 期貨、選擇權 daily data、即時報價、交易明細。
- 國際市場 : 美股股價 daily、minute、美國債券殖利率、貨幣發行量(美國)、黃金價格、原油價格、G8 央行利率、G8 匯率、

### 4種API

#### 	login

```apl
POST https://api.finmindtrade.com/api/v4/login
```

| 參數名稱 | 資料型態 | 必填 | 說明                 |
| -------- | -------- | ---- | -------------------- |
| user_id  | String   | N    | 使用者 id ，申辦帳號 |
| password | String   | N    | 使用者密碼，申辦帳號 |

#### 	獲取資料 API

```apl
GET: https://api.finmindtrade.com/api/v4/data
```

| 參數名稱   | 資料型態 | 必填 | 說明                                                         |
| ---------- | -------- | ---- | ------------------------------------------------------------ |
| dataset    | String   | Y    | 資料集名稱                                                   |
| data_id    | String   | N    | 資料代碼                                                     |
| start_date | String   | N    | 起始時間，必須要跟end_date一起使用                           |
| end_date   | String   | N    | 結束時間，必須要跟end_date一起使用                           |
| token      | String   | N    | 使用者 token，請先[登入](https://finmind.github.io/login/)拿到 token |

#### 	查詢資料參數清單 API

```apl
GET: https://api.finmindtrade.com/api/v4/datalist?
```

| 參數名稱 | 資料型態 | 必填 | 說明                 |
| -------- | -------- | ---- | -------------------- |
| dataset  | String   | Y    | 資料集名稱           |
| user_id  | String   | N    | 使用者 id ，申辦帳號 |
| password | String   | N    | 使用者密碼，申辦帳號 |

​	查詢欄位名稱中英對照 API

```apl
GET: https://api.finmindtrade.com/api/v4/translation?
```

| 參數名稱 | 資料型態 | 必填 | 說明                 |
| -------- | -------- | ---- | -------------------- |
| dataset  | String   | Y    | 資料集名稱           |
| user_id  | String   | N    | 使用者 id ，申辦帳號 |
| password | String   | N    | 使用者密碼，申辦帳號 |

### python

```Python
import requests
import pandas as pd
```

### 股價日成交資訊

```python
url = "https://api.finmindtrade.com/api/v4/data"
parameter = {
    "dataset": "TaiwanStockPrice",
    "data_id": "2330",
    "start_date": "2021-09-13",
    "end_date": "2021-09-17"
}
r = requests.get(url, params=parameter)
data = r.json()
stock_deal_info = data["data"]
print(pd.DataFrame(stock_deal_info))
```

顯示日期、股票代碼、成交金額、成交量、時間、漲跌(1:漲；2跌)

### 股票當日逐筆成交資訊

```python
url = "https://api.finmindtrade.com/api/v4/data"
parameter = {
    "dataset": "TaiwanStockPriceTick",
    "data_id": "2330",
    "start_date": "2021-09-27"
}
r = requests.get(url, params=parameter)
data = r.json()
stock_history_info = data["data"]
print(pd.DataFrame(stock_history_info).tail())  # 顯示最後五筆資料
```

### 股票綜合損益表

```python
url = "https://api.finmindtrade.com/api/v4/data"
parameter = {
    "dataset": "TaiwanStockFinancialStatements",
    "data_id": "2330",
    "start_date": "2021-06-01",
    "end_date": "2021-09-01"  # 不加則抓到現在日期
}
r = requests.get(url, params=parameter)
data = r.json()
stock_income_statement = data["data"]
print(pd.DataFrame(stock_income_statement))
```

### 股利政策表

```python
url = "https://api.finmindtrade.com/api/v4/data"
parameter = {
    "dataset": "TaiwanStockDividend",
    "data_id": "2330",
    "start_date": "2021-01-01"
}
r = requests.get(url, params=parameter)
data = r.json()
stock_dividend = data["data"]
print(pd.DataFrame(stock_dividend).head())  # 顯示前五筆資料
```



### 參考資料

https://finmindtrade.com/

https://blog.jiatool.com/posts/stock_finmind/?fbclid=IwAR3_2BFE6vT0kSoHmlNdPpVRmjeUj75h-uxAgaccmRK0DFbYO1x8acNDRHk

https://github.com/FinMind/FinMind/
