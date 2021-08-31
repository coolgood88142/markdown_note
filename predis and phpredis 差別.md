### predis and phpredis 差別

predis 是使用纯 php寫的，phpredis 是用c語言寫php 擴充套件。

predis 是PHP的redis的套件，並且用socket連線，可以不用設定配置，執行用composer安裝即可。

phpredis是使用c語言的php的pecl，可以直接用pecl提供相關的function。

兩者最大的區別是連線方式，phpredis要用擴充套件的php-fpm連線到redis，每次請求連線都是用同一個，predis則是用php的socket來做連線。

laravel官網會推薦predis，是因為用純PHP的原因，可以composer安裝，可方便在laravel上實做。

|            | predis | phpredis |
| ---------- | ------ | -------- |
| 語言       | php    | C語言    |
| 連線方式   | socket | php-fpm  |
| 長時間連線 | X      | V        |
| 速度       | X      | V        |

https://blog.csdn.net/zhezhebie/article/details/117924530

https://eyehere.net/2015/phpredis-vs-predis/

https://blog.51cto.com/u_13238147/2547532

