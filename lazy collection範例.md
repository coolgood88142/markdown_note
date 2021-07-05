### lazy collection範例

##### 目的：想要將post的10萬筆資料，存取到變數裡，做排序和每一筆資料的id做加總，但是不想要讓記憶體那麼大

- 排序
  - 使用cursor()，做排序
  - 這時顯示的記憶體會是18M而不是220M

- 加總
  - 使用cursor()->groupBy(['id'])去跑map，這時post會有10萬的id資料
  - 用sum()做加總，這時顯示的記憶體會是18M而不是84M

1. #### 為什麼cursor程式碼 在加上$sum = $post->sum()，18M變成84M，正常要維持18M才對

   因為lazy collection物件，算是collection的一種，如果用sum的話，就是用collection做加總，而不是lazy collection，所以存取變數的記憶體，才會變大

2. #### 為什麼加上groupBy(['id'])之後，不能使用?

   要改用sortBy(['id'])，才可以針對collection的id做排序

3. #### 為什麼用lazy collection ，可以讓記憶體不要那麼大，原因是什麼?

   lazy collection主要是用yield，迴圈每跑一次遇到yield，回傳相同的Generator物件，而不是資料，
   所以每跑一次迴圈，只會占用到相同的記憶體，記憶體就不會一直壘加

4. #### 要解釋lazy collection 的原理，為什麼10萬筆220M使用lazy conction，就變成18M，為什麼會變成這樣?

   post原本的物件，lazy collection主要是用yield，迴圈每跑一次會拿到相同的Generator物件，只會一直占用相同的記憶體，不會壘加，對應到範例的10萬筆220M記憶體，使用laravel的cursor()，會變成lazy collection物件，在建立lazy collection時，會一直占用Post的記憶體，消耗記憶體較小，才會變成18M

5. #### lazy collection-takeUntilTimeout

   可以設定某個時間，將迴圈中當前的資料，新增到集合

   ```php
   $post = Post::cursor()->takeUntilTimeout(now()->addMinute());
   $post->each(function ($number) {
   	dump($number);
       sleep(1);
   });
   ```

   例如：Post的lazy collection物件，設定每分鐘新增到集合，在用迴圈用dump顯示物件明細，每跑一次等待1秒在執行

6. #### lazy collection-tapEach

   跑迴圈時將每筆資料，從lazycollection集合中釋放掉

   ```php
   $post = Post::cursor()->tapEach(function ($value) {
          dd($value);
   });
   ```

   例如：Post的lazy collection物件，每跑一次當前的資料就釋放掉，所以寫dd($value)，並不會顯示value資料

7. #### lazy collection-remember

   會把lazy collection集合中的所有值，記到cache裡

   ```php
   $post = Post::cursor()->remember();
   $post->take(1)->all();
   $post->take(3)->all();
   ```

   例如：Post的lazy collection物件，已經將集合中全部的資料，記到cache裡，在用take取第1-第3筆資料