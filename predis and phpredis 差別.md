### predis and phpredis 差別



phpredis 是用c語言寫php 擴充套件，predis 是使用纯 php寫的。

兩者最大的區別是連線方式，phpredis要用擴充套件的php-fpm連線到redis，每次請求連線都是用同一個，predis則是用php的socket來做連線。

雖然官網會推薦predis，是因為用純PHP的原因，可以composer安裝，可方便在laravel上實做。





————————————————

[PhpRedis](https://github.com/phpredis/phpredis)是用於與[Redis](https://redis.io/)存儲通信的 PHP 擴展。

[Predis](https://github.com/nrk/predis)是純 PHP 上 PhpRedis 的替代品，默認情況下不需要任何額外的 C 擴展，但它可以選擇與[phpiredis](https://github.com/nrk/phpiredis)配對。

phpRedis是C实现的一个php的pecl扩展，php代码里直接使用该扩展提供的相关类、方法即可。

phpRedis扩展的编译安装没什么特别的，下载下来解压phpize、make、make install，配置文件里配置下就ok了，这个扩展本身的安装没有外部依赖，因为内部实现使用了php层封装的streem流操作。

|      | predis | phpredis |
| ---- | ------ | -------- |
|      |        |          |
|      |        |          |
|      |        |          |



需要範例