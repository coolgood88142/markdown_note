---
title: "python部署至heroku"
date: "2019-09-28"
author: "python"
summary: "說明使用python的flask套件，怎麼部署在heroku上"
---

![python](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/python.png)

## 安裝[python](https://www.python.org/downloads/)

安裝完之後，打開cmd確認，是否安裝成功

```bash
python --version
Python 3.7.4
```

## 安裝HeroKu

#### 1.註冊[HeroKu](<https://id.heroku.com/login>)帳號

#### 2.安裝[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#windows)，用cmd執行HeroKu

```bash
heroku
```



#### 3.登入剛剛註冊的HeroKu帳號

```bash
heroku login
Email: coolgood88142@gmail.com
Password: xxxxxxx
Logged in as coolgood88142@gmail.com #登入成功
```



#### 4.安裝[Git](https://git-scm.com/downloads)



#### 5.HeroKu建立專案目錄
```bash
heroku create
Creating app... done, ⬢ aqueous-eyrie-33328
https://aqueous-eyrie-33328.herokuapp.com/ | https://git.heroku.com/aqueous-eyrie-33328.git
```

aqueous-eyrie-33328代表，在heroku的專案名稱，稍後將專案上傳到heroku



## 安裝virtualenv

在自己的專案，安裝virtualenv，用`pip`指令安裝

```bash
pip install virtualenv
```



## 建立虛擬環境

建立虛擬環境，資料夾名稱為venv，安裝好之後進去venv/Scripts路徑底下，然後開啟虛擬環境打`activate`進入

```bash
virtualenv venv
cd venv/Scripts
activate
```

如果使用者用windows系統的Git bash，會顯示`activate: command not found`錯誤，這句指令是要執行路徑有個`activate.bat`檔案，指令要改成下面內容。

```bash
source ./activate
```

進入虛擬環境之後，路徑旁邊會顯示剛剛安裝虛擬環境的資料夾

```bash
(venv) C:\xampp\htdocs\test_python\venv\Scripts
```

安裝Flask Python套件，包含flask、gunicorn、jinja2，flask是python的框架，用來安裝web環境，gunicorn是適用於Python WSGI HTTP 伺服器，支援框架運作，jinja2是python的板模套件，主要是頁面(html)，使用變數顯示頁面，不需要在寫值傳到前端。

```bash
pip install flask gunicorn jinja2
```

 將路徑回上兩層 

```
cd ../..
(venv) C:\xampp\htdocs\test_python
```



## 建立app.py

將路徑回到專案底下並建立一個檔案為app.py，內容如下

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

在本機執行app.py，打pytohn指令:`python app.py`，此時系統顯示`http://127.0.0.1:5000/`網址，內容為Hello World!

```bash
python app.py
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

![test_python.PNG](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/test_python.PNG)



## 建立requirements檔案

```bash
pip freeze > requirements.txt
```

主要是將虛擬環境目前所有的套件，記錄到`requirements.txt`，內容如下：

```python
Click==7.0
Flask==1.1.1
gunicorn==19.9.0
itsdangerous==1.1.0
Jinja2==2.10.1
MarkupSafe==1.1.1
Werkzeug==0.16.0
```

顯示安裝的套件名稱與版本，如果不想一個一個安裝的話，可以使用`requirements.txt`檔案，放在自己專案裡，打上`pip install -r requirements.txt`指令，系統會自動安裝套件。

當專案上傳至hoerku時，系統會建立虛擬環境，需要`requirements.txt`檔案進行套件安裝。

建立好了之後打`deactivate`，離開虛擬環境

```
deactivate
```



## 建立gitignore檔案

打開`.gitignore`文件並寫入`venv`，在上傳Heroku到時忽略`venv`，因為Heroku不需要使用`venv`，直接在`requirements.txt`文件，找出專案上有使用哪些套件，系統會依據文件內容自動安裝套件。



## 建立Procfile

每個部署到Heroku的Flask應用程序都需要一個Procfile，是為了要告訴heroku要怎麼執行專案

```python
web: gunicorn app:app
```

`web: gunicorn app:app`是執行app.py檔案裡的一個名為app的變數

透過gunicorn套件告訴heroku，將flask框架建立web server環境，如果不建立的話，heroku不知道怎麼把專案要怎麼啟動。



## 上傳至Heroku

```bash
git init
git remote add heroku https://git.heroku.com/aqueous-eyrie-33328.git
git add .
git commit -m "my-first-python"
git push heroku master
```

上傳完之後，打上`https://aqueous-eyrie-33328.herokuapp.com/`網址，確認有頁面就完成了。

![python_web](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/python_web.PNG)



## 設定ClearDB MySQL

用專案上的conn.py檔案，先建立DB連線，在建立資料表

補上圖片







## 設定信用卡

要設定信用卡才可以用 `ClearDB MySQL`

![card](C:\xampp\htdocs\markdown_note\assets\images\card.PNG)



## 問題

#### 為什麼在本機執行python app.py，可以執行web環境，但是heroku的Procfile卻不行?

本機執行`app.py`，是`flask`會透過瀏覽器執行web server環境，來執行檔案內容指定網址，在網頁上呈現或做什麼事情。

但是`heroku`有執行檔案，確認不知道用什麼方式啟動web server環境。



參考資料:

http://www.gtlambert.com/blog/deploy-flask-app-to-heroku、

https://www.evanlin.com/python-e5-a6-82-e4-bd-95-e5-9c-a8heroku-e4-b8-8a-e9-9d-a2-e5-bb-ba-e7-ab-8b-e7-ac-ac-e4-b8-80-e5-80-8bpython-app/、

https://stackoverflow.com/questions/22397521/execute-commands-from-bat-file-on-git-bash-terminal/39032987