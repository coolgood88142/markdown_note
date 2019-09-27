---
title: "python部署至heroku"
date: "2019-09-28"
author: "python"
summary: "說明將python部署至heroku的過程"
---

## 安裝[python](https://www.python.org/downloads/)

安裝完之後，打開cmd確認，是否安裝成功

```
python --version
Python 3.7.4
```

## 安裝HeroKu

#### 1.註冊[HeroKu](<https://id.heroku.com/login>)帳號

#### 2.安裝[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#windows)，用cmd執行HeroKu

```
heroku
```



#### 3.登入剛剛註冊的HeroKu帳號

```
heroku login
Email: coolgood88142@gmail.com
Password: xxxxxxx
Logged in as coolgood88142@gmail.com #登入成功
```



#### 4.安裝[Git](https://git-scm.com/downloads)，安裝好之後用cmd執行Git

```
git
```



#### 5.登入HeroKu網站，在Personal新增create new app

![heroku-1](https://coolgood88142.github.io/images/heroku-1.png)



#### 6.從HeroKu網站裡的Setting找HeroKu Git URL並複製，代表app的連結

![heroku-2](https://coolgood88142.github.io/images/heroku-2.png)



#### 7.將專案上版至HeroKu

```
git init
git remote add origin https://git.heroku.com/quiet-crag-79025.git
git add .
git commit -m "my-first-website"
git push origin master
```



## 安裝virtualenv

在自己的專案，安裝virtualenv，用pip指令執行

```
pip install virtualenv
```



## 建立虛擬環境

建立虛擬環境資料夾名稱為venv，安裝好之後進去venv/Scripts路徑底下，然後開啟虛擬環境打(activate)進入

```
virtualenv venv
cd venv/Scripts
activate
```

進入虛擬環境之後，路徑旁邊會顯示剛剛安裝虛擬環境的資料夾，

```
(venv) C:\xampp\htdocs\test_python\venv\Scripts>
```

安裝Flask Python套件，包含flask、gunicorn、jinja2，安裝好了之後打deactivate，離開虛擬環境

```
pip install flask gunicorn jinja2
deactivate
```



## 建立app.py

將路經回到專案底下並建立一個檔案為app.py，內容如下

```
new-item app.py
```

app.py內容

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

在本機執行app.py，打pytohn指令:python app.py，此時系統顯示http://127.0.0.1:5000/網址，內容為Hello World!

```
python app.py
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

![](https://coolgood88142.github.io/images/test_python.PNG)



## 建立Procfile

建立Procfile檔案，是為了要告訴heroku要怎麼執行這個專案





參考資料:

http://www.gtlambert.com/blog/deploy-flask-app-to-heroku