### lazy collection範例

##### 目的：想要將post的10萬筆資料，存取到變數裡，做排序和每一筆資料的id做加總，但是不想要讓記憶體那麼大

- 排序
  - 使用cursor()，做排序
  - 這時顯示的記憶體會是18M而不是220M

- 加總
  - 使用cursor()->groupBy(['id'])去跑map，這時post會有10萬的id資料
  - 用sum()做加總，這時顯示的記憶體會是18M而不是84M

1. #### 為什麼cursor程式碼 在加上$sum = $post->sum()，18M變成84M，正常要維持18M才對

   重做

   

   

   lazy collection算是collection的一種，如果用sum的話，就是用collection做加總，而不是lazy collection，所以存取變數的記憶體，才會變大。

   

   我們可以自行建立laztcollection，使用yieldt回傳物件，在跑迴圈做加總

   去找cursor的LazyCollection，怎麼做到節省記憶體，yield的用法是怎麼做的

   cursor跟laravel的chunk很類似，做分批查詢，但是cursor是每次回傳LazyCollection物件，例如有10萬筆資料，每跑一次就多一個Eloquent  model，將10萬個Eloquent  model同時存到一個記憶體，所以才會節省很多。

   ```php
   $data = LazyCollection::make(function() {   
               $posts = Post::all();
               
               foreach ($posts as $post){ 
                   yield $post->id;
               }
           });
   
   $sum = $data->sum();
   ```

2. #### 為什麼加上groupBy(['id'])之後，不能使用?

   重做

   要改用sortBy('id')，才可以用lazy collection的id做排序

3. #### 為什麼用lazy collection ，可以讓記憶體不要那麼大，原因是什麼?

   lazy collection主要是用yield，迴圈每跑一次遇到yield，會回傳相同的Generator物件，而不是資料，
   所以只會占用到相同的記憶體，記憶體就不會一直壘加

4. #### 要解釋lazy collection 的原理，為什麼10萬筆220M使用lazy conction，就變成18M，為什麼會變成這樣?

   post的lazy collection主要是用yield，迴圈每跑一次，會回傳相同的Generator物件，就只會一直占用相同的記憶體，不會壘加，對應到範例的10萬筆220M記憶體，使用laravel的cursor()，會變成lazy collection物件，

   

   在建立lazy collection時，會一直占用Post的記憶體，消耗記憶體較小，才會變成18M

5. #### lazy collection-takeUntilTimeout

   可以設定某個時間，將迴圈中當前的資料，新增到集合

   去找3種lazycollection的fucntion什麼情況下使用

   ```php
   $post = Post::cursor()->takeUntilTimeout(now()->addMinute());
   $post->each(function ($number) {
   	dump($number);
       sleep(1);
   });
   ```

   例如：Post的lazy collection物件，設定每分鐘新增到集合，在用迴圈用dump顯示物件明細，每跑一次等待1秒在執行

6. #### lazy collection-tapEach

   可以設定collection物件，要給予什麼資料

   ```php
   $post = Post::cursor()->tapEach(function ($value) {
          dump($value->id);
   });
   
   $data = $post->take(3)->all();
   ```

   例如：Post的lazy collection物件，設定每筆資料回傳id，在使用`take()`顯示前3筆資料，來顯示前3筆的id

7. #### lazy collection-remember

   記住lazy collection集合中的所有資料，

   ```php
   $post = Post::cursor()->remember();
   $post->take(3)->all();
   $post->take(10)->all();
   ```

   例如：Post的lazy collection物件，記住集合中全部的資料，在用`take(3)`將前3筆資料，記到cache裡，在執行`take(10)`時，前3筆的資料會從cache裡拿，如果資料越多會更明顯讀取資料的速度。
   
   

參考資料：

- https://ithelp.ithome.com.tw/articles/10194457
- https://laravel.com/api/8.x/Illuminate/Support/LazyCollection.html#method_takeUntilTimeout
- https://laravel.com/api/8.x/Illuminate/Support/LazyCollection.html#method_tapEach
- https://laravel.com/api/8.x/Illuminate/Support/LazyCollection.html#method_remember
- https://tn710617.github.io/zh-tw/laravel-digging-deeper-collections/
- https://laravel.com/docs/8.x/eloquent#streaming-results-lazily

