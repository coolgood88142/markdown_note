---
title: "PHP Unit"
date: "2021-06-08"
author: "PHP Unit"
summary: "介紹用laravel使用PHP Unit"
---

#### 介紹

在介紹之前講單元測試、整合測試

- 單元測試：是指測試程式中最小的單位，包含function、class、Object等等，所以我們要寫一支測試的程式中，會有很多個單元測試。
  
  ```php
  class ComputerTest extends TestCase
  {
      /**
       * A basic feature test example.
       *
       * @return void
       */
      public function testComputer()
      {
          $response = $this->get('/computer');
          $response->assertStatus(200);
      }
  }
  ```
  
  以上範例，測試ComputerTest的testComputer()，確認route的computer，回傳的Status code是否為200

- 整合測試：是測試的程式與外部的關係，例如：兩支程式的關係、資料庫、session、cookie等等，通常會測試兩支程式的關聯，是否沒問題。
  
  ```php
  class ComputerServiceTest extends TestCase
  {
      /**
       * A basic unit test example.
       *
       * @return void
       */
      public function testComputer()
      {
          self::assertIsString(
              (new ComputerService())->test_plus(2, 3)
          );
      }
  }
  ```
  
  以上範例，ComputerServiceTest的testComputer()，執行ComputerService的test_plus()，將2 + 3做加法

#### TDD

開發團隊在開發前，會先寫測試再開發，這種步驟稱為TDD(Test-Driven Development)，稱為測試驅動開發，有助於釐清撰寫程式的設計，測試程式的好處，是經過測試得知的結果，來驗證程式是否符合預期，以下介紹TDD分為5個步驟

1. 選定功能，新增測試案例
   
   - 設計怎麼使用測試程式
   - 此階段尚未撰寫程式

2. 執行測試，得到Failed(紅燈)
   
   - 設計初步測試程式，沒有撰寫測試功能
   - 確認測試程式可以使用

3. 實作「夠用」的產生程式
   
   - 實做測試功能，不需要過多個程式碼，只要測試功能正常即可
   - 用最快的速度通過測試

4. 再做執行測試，得到Passed(綠燈)
   
   - 確保已完成執行測試程式
   - 此階段可以開始撰寫開發程式，並寫測試程式與開發程式可正常運作

5. 重構程式
   
   - 優化測試功能，可更好維護測試功能

PHP Unit是一個可以將PHP的程式進行單位測試或整合測試，來驗證自己寫的function有沒有問題，在我們新增程式或修改程式時，能確保不會影響到原本的功能，一般都會先寫好測試，才會開始寫功能需求。

laravel本身就包含PHP Unit，會一個phpunit.xml做配置，分別在`Feature`和`Unit`兩個目錄裡，在執行測試的時候，並不會啟動專案的server，所以寫測試function時，要先寫好預設資料或要帶的參數來做測試。

#### 指令

1. 建立測試檔案，例如：BasicTest
   
   ```bash
   php artisan make:test BasicTest 在test/Feature資料夾裡新增檔案
   php artisan make:test BasicTest --unit  要補上unit才會新增到test/FUnit資料夾裡
   ```

2. 測試全部class
   
   ```bash
   ./vendor/bin/phpunit 這個指令會顯示很詳細的資訊
   php artisan test 顯示簡化的資訊
   ```

3. 測試Feature或Unit全部的class
   
   ```bash
   php artisan test --testsuite=Feature
   php artisan test --testsuite=Unit
   ```

4. 測試某個class，例如：測試Unit/BasicTest
   
   ```bash
   php artisan test --filter 'Tests\\Unit\\BasicTest'
   
   //如果加function名稱，可以只測試某個class的某個function
   php artisan test --filter 'Tests\\Unit\\BasicTest::test_example'
   ```

#### Http Test

向http做請求，檢查響應的stats code

1. Making Requests
   
   ```php
   //用get方式發出請求，在確認回傳的status code是否為200
   public function test_a_basic_request()
   {
       //測試route localhost
       $response = $this->get('/');
   
       //測試localhost的status code是否為200
       $response->assertStatus(200);
   }
   
   //自行定義表頭
   public function test_interacting_with_headers()
    {
        $response = $this->withHeaders([
            'X-Header' => 'Value',
        ])->get('/user', ['name' => 'testName']);
   
        $response->assertStatus(201);
    }
   
    //cookie
    public function test_interacting_with_cookies()
    {
        //測試localhost的cookies參數，color的值為blue和name的值為Taylor
        $response = $this->withCookies([
            'color' => 'blue',
            'name' => 'Taylor',
        ])->get('/');
    }
   
    //session
    public function test_interacting_with_the_session()
    {
        $response = $this->withSession(['banned' => false])->get('/');
    }
   
    //dump，顯示表頭、session、頁面標籤的資訊
    public function test_basic_test()
    {
        //測試route localhost
        $response = $this->get('/');
   
        //顯示localhost的Header物件內容
        $response->dumpHeaders();
   
        //顯示localhost的session物件內容
        $response->dumpSession();
   
        //整個顯示localhost的response物件
        $response->dump();
    }
   
   //用route驗證加法、減法、乘法、除法
       public function test_plus(){
           $a = 1;
           $b = 2;
   
           //呼叫route plus，使用POST，帶test1與test2參數
           $response = $this->call('POST', '/plus', ['test1' => $a, 'test2' => $b]);
   
           //測試上一句的route plus，status code是否為200
           $this->assertEquals(200, $response->status());
       }
   
       public function test_toSubtract(){
           $a = 5;
           $b = 2;
   
           //呼叫route toSubtract，使用POST，帶test1與test2參數
           $response = $this->call('POST', '/toSubtract', ['test1' => $a, 'test2' => $b]);
   
           //測試上一句的route plus，status code是否為200
           $this->assertEquals(200, $response->status());
       }
   
       public function test_multiply(){
           $a = 4;
           $b = 3;
   
           //呼叫route multiply，使用POST，帶test1與test2參數
           $response = $this->call('POST', '/multiply', ['test1' => $a, 'test2' => $b]);
   
           //測試上一句的route plus，status code是否為200
           $this->assertEquals(200, $response->status());
       }
   
       public function test_divided(){
           $a = 10;
           $b = 2;
   
           //呼叫route divided，使用POST，帶test1與test2參數
           $response = $this->call('POST', '/divided', ['test1' => $a, 'test2' => $b]);
   
           //測試上一句的route plus，status code是否為200
           $this->assertEquals(200, $response->status());
       }
   ```

2. Testing JSON APIs
   測試執行API回傳json格式
   
   ```php
   public function test_making_an_api_request()
    {
        $response = $this->postJson('/api/user', ['name' => 'testName']);
   
        $response
            ->assertStatus(201)
            ->assertJson([
                'created' => true,
            ]);
    }
   
    public function test_fluent_json()
    {
        $response = $this->json('GET', '/users/1');
   
        $response
            ->assertJson(fn (AssertableJson $json) =>
                $json->where('id', 1)
                     ->where('name', 'Victoria Faith')
                     ->missing('password')
                     ->etc()
            );
    }
   ```

3. Testing File Uploads
   測試上傳檔案
   
   ```php
   public function test_avatars_can_be_uploaded()
   {
        Storage::fake('uploaded');
   
        $file = UploadedFile::fake()->image('test.jpg');
   
        $response = $this->post('/testUploaded', [
            'uploaded' => $file,
        ]);
   }
   ```

4. Testing Views
   測試頁面
   
   ```php
   public function test_a_welcome_view_can_be_rendered()
   {
        $view = $this->view('welcome', ['name' => 'testName']);
   
        $view->assertSee('Taylor');
   }
   ```

```
HTTP Test適合測試請求網頁做回傳，來確認有沒有問題，例如route的回傳的資料或狀態，以及API

#### Console Test

測試輸出訊息

```php
public function test_console_command()
{
 //測試訊息名稱為question
 $this->artisan('question')

 //建立問題What is your name?(你叫什麼名字?)，答：Taylor Otwell
 ->expectsQuestion('What is your name?', 'Taylor Otwell')

 //建立問題Which language do you prefer?(你喜歡哪種程式語言?)，答：PHP
 ->expectsQuestion('Which language do you prefer?', 'PHP')

 //顯示你要的訊息Your name is Taylor Otwell and you prefer PHP
 ->expectsOutput('Your name is Taylor Otwell and you prefer PHP.')

 //顯示測試訊息Your name is Taylor Otwell and you prefer Ruby.
 ->doesntExpectOutput('Your name is Taylor Otwell and you prefer Ruby.')

 //結束
 ->assertExitCode(0);

 //測試訊息名稱為module:import
 $this->artisan('module:import')
     //建立問題Do you really wish to run this command?答：no
     ->expectsConfirmation('Do you really wish to run this command?', 'no')
     //結束
     ->assertExitCode(1);
}
```

Console Test適合console的設定輸入訊息

#### Laravel Dusk

laravel duck是一個能用瀏覽器測試API，只需要額外安裝ChromeDriver，就可以兼容瀏覽器

安裝套件

```bash
composer require --dev laravel/dusk
```

安裝之後，要在執行指令，會在tests/Browser建立ChromeDriver

```bash
php artisan dusk:install
```

如果要使用其他瀏覽器，打開test/DuskTestCase.php，將`startChromeDriver()`註解起來

```php
public static function prepare()
 {
    if (! static::runningInSail()) {
        //static::startChromeDriver();
    }
}
```

在修改`driver()`程式碼，回傳指定的route

```php
protected function driver()
{
    return RemoteWebDriver::create(
        'http://localhost:4444/wd/hub', DesiredCapabilities::phantomjs()
    );
}
```

建立dusk檔案，例如：LoginTest

```bash
php artisan dusk:make LoginTest
```

執行測試Dusk

```bash
php artisan dusk
```

建立瀏覽器

```php
public function test_basic_example()
{
    $user = User::factory()->create([\
        'email' => 'taylor@laravel.com',
    ]);

    $this->browse(function ($browser) use ($user) {
        $browser->visit('/login')
            ->type('email', $user->email)
            ->type('password', 'password')
            ->press('Login')
            ->assertPathIs('/home');
    });
}
```

以上範例，是在建立一個User的資料，之後在進到登入畫面，在夾帶email、password的資料做登入，如果成功就進入home這個route

route的用法

```php
$browser->visit('/login');     //訪問route
$browser->visitRoute('login'); //訪問命名的route
$browser->back();               //回上一頁
$browserforward();          //回下一頁
$browser->refresh();           //重新整理
$browser->resize(1920, 1080); //設定瀏覽器大小
```

laravel Duck適合測試網頁版面，可以寫css來測試樣板，還有route的控制，可以用驗證、javascript、導頁等等。

#### DataBase Test

我們在每次測試資料時，都會重置資料庫，不會影響到現有的資料，在寫測試時要使用`Illuminate\Foundation\Testing\RefreshDatabase`做重置，會用laravel的Factory、Model、Seed來做測試

我們可以在factory寫`configure()`在建立時，要做什麼事情

```php
class UserFactory extends Factory
{
    /**
     * The name of the factory's corresponding model.
     *
     * @var string
     */
    protected $model = User::class;

    /**
     * Configure the model factory
     *
     * @return $this
     */
    public function configure()
    {
        //執行make model要做的事情
        return $this->afterMaking(function (User $user) {
            //
        //執行create model 後要做的事
        })->afterCreating(function (User $user) {
            //
        });
    }

    // ...
```

用seeder建立資料庫來做測試

```php
class ExampleTest extends TestCase
{
    use RefreshDatabase;

    /**
     * Test creating a new order.
     *
     * @return void
     */
    public function test_orders_can_be_created()
    {
        // 執行建立所有的Seeder檔案
        $this->seed();

        // 執行建立OrderStatusSeeder
        $this->seed(OrderStatusSeeder::class);

        // ...
    }
}
```

測試資料庫中已經建立幾筆資料，例如：tables users有5筆資料

```php
$this->assertDatabaseCount('users', 5);
```

測試table確認有這筆資料，例如：tables users的email欄位，有沒有sally@example.com這筆資料

```php
$this->assertDatabaseHas('users', [
    'email' => 'sally@example.com',
]);
```

如果要確認沒有這筆資料，要改用assertDatabaseMissing

測試table刪除資料，例如：tables users第一筆資料是否被刪除

```php
use App\Models\User;

$user = User::find(1);

$user->delete();

$this->assertDeleted($user);
```

測試table是否已經被刪除，例如：users這張table是否被刪除了

```php
$this->assertSoftDeleted($user);
```

#### 範例：我們建立一個測試假資料寫入到db

1. 先建立測試檔案
   
   ```bash
   php artisan make:test PostTest --unit
   ```
   
   ```php
   app/Post.php
   public static function archives()
   {
       return static::selectRaw('year(created_at) year, monthname(created_at) month, count(*) published')
           ->groupBy('year', 'month')
           ->orderByRaw('min(created_at) desc')
           ->get()
           ->toArray();
   }
   ```

2. 建立factory
   
   ```bash
   php artisan make:factory PostFactory --model=Post
   ```
   
   在利用laravel的Faker，自動產生資料庫的資料
   
   ```php
   $factory->define(App\Post::class, function (Faker $faker) {
       return [
           'user_id' => function () {
               return factory(App\User::class)->create()->id;
           },
           'title' => $faker->sentence,
           'body' => $faker->paragraph
       ];
   });
   ```
   
   3. 建立測試function
      
      ```php
      class PostTest extends TestCase
      {
        use RefreshDatabase;
      
        public function testArchives()
        {
            // Given I have two records in the database that art posts,
            // and each one is posted a month apart.
            $first = factory(Post::class)->create();
            $second = factory(Post::class)->create([
               'created_at' => \Carbon\Carbon::now()->subMonth()
            ]);
      
            // When I fetch the archives.
            $posts = Post::archives(spy);
      
            // Then the response should be in the proper format.
            $this->assertCount(2, $posts);
        }
      }
      ```

3. 執行指令
   
   ```bash
   php artisan test --filter 'Tests\\Unit\\PostTest::testArchives'
   ```
   
   這裡要注意 laravel 8版本需要，在執行前需要安裝legacy-factories
   
   ```
   composer require laravel/legacy-factories
   ```

以上範例，測試Post table新增兩筆資料，第二筆的時間改成下個月，在用`archives()`依照建立日期做查詢，用年月做排序，最後再用assertCount確認，是否要成功新增2筆。

這裡要注意的事，PostTest有加上`RefreshDatabase`，每次執行測試時，Post table就會重置。

#### 範例：我們測試用POST請求來建立資料

1. 建立測試檔案
   
   ```bash
   php artisan make:test InsertionTest
   ```

2. 更換下面的程式碼
   
   ```php
   public function test_that_a_task_can_be_added()
   {
       $this->withoutExceptionHandling();
       $response = $this->post('/api/tasks/create', [
           'name' => 'Write Article',
           'description' => 'Write and publish an article'
       ]);
       $response->assertStatus(200);
       $this->assertTrue(count(Task::all()) > 1);
   }
   ```
   
   先執行`withoutExceptionHandling()`，會列出所有PHPUnit會拋錯的訊息，以方便追蹤程式，在去做post請求`tasks/create`，帶`name`、`description`兩個參數，在用assertStatus確認response的status code是否為200，最後再用assertTrue，確認Task的資料是否有一筆以上。

3. 建立route
   
   ```php
   Route::prefix('task')->group(function () {
       Route::get('/{task}', [TaskController::class, 'show']);
       Route::post('/create', [TaskController::class, 'create_task']);
       Route::patch('{task}/complete', [TaskController::class, 'mark_task_as_completed']);
       Route::delete('/{id}', [TaskController::class, 'destroy']);
   });
   ```
   
   在rotues/web.php，新增下面這段程式碼

4. 建立Model、Controllers、Factory、Migration、Seeders
   
   ```bash
   php artisan make:model Task -a
   ```
   
   執行上面的指定會自動建立 `App\Models\Task.php`、`App\Http\Controllers\TaskController.php`、`Database\Factories\TaskFactory.php`、`Database\migrations\CreateTasksTable.php`、`Database\Seeders\TaskSeeder.php`

5. 建立Table
   
   ```php
   class CreateTasksTable extends Migration
   {
       public function up()
       {
           Schema::create('task', function (Blueprint $table) {
                $table->id();
                $table->string('name');
                $table->text('description');
                $table->boolean('completed')->default(0);
                $table->timestamps();
           });
       }
   }
   ```
   
   Tasks table建立name、description、completed等3個欄位，將CreateTasksTable.php的up()，調整成上面這段程式，在執行下面這段指令
   
   ```bash
   php artisan migrate
   ```

6. 調整Model
   
   ```php
   class Task extends Model
   {
       protected $fillable = ['name', 'description', 'status'];
   
       public function mark_task_as_completed()
       {
           $this->completed = 1;
           $this->save();
       }
   
       public function is_completed()
       {
           return $this->completed;
       }
   }
   ```

7. 調整Factory
   
   ```php
   class TaskFactory extends Factory
   {
       public function definition()
       {
           return [
               'name' => $this->faker->sentence(4),
               'description' => $this->faker->sentence(20),
               'completed' => random_int(0, 1)
           ];
       }
   }
   ```
   
   建立假資料，`name`指定4個字元、`description`指定20個字元、`completed`產生0或1的數字

8. 調整Controller
   
   ```php
   class TaskController extends Controller
   {
       public function destroy($task)
       {
           //查詢task第一筆資料後執行刪除，在回傳Response物件，設定status code為410，組message為task deleted successfully
           $task_to_be_deleted = Task::findOrFail($task);
           $task_to_be_deleted->delete();
           return response()->json([
               'message' => 'task deleted successfully'
           ], 410);
       }
   
       public function mark_task_as_completed(Task $task)
       {
           //執行model-task的mark_task_as_completed()在回傳Response物件，設定status code為200，組message為task deleted successfully marked as updated
           $task->mark_task_as_completed(); 
           return response()->json([
               'message' => 'task successfully marked as updated'
           ], 200);
       }
   
       public function create_task(Request $request)
       {
           DB::table('task')->insert([
               'name' => $request->name,
               'description' => $request->description,
               'completed' => random_int(0, 1)
           ]);
   
           return 'success';
       }
   }
   ```

9. 補上測試function
   
   ```php
   class InsertionTest extends TestCase
   {
       public function test_that_a_task_can_be_added()
       {
           //使用withoutExceptionHandling()時，會顯示所有錯誤訊息
           $this->withoutExceptionHandling();
           //建立response物件，執行post task/create route，帶name、description兩個參數
           $response = $this->post('/task/create', [
               'name' => 'Write Article',
               'description' => 'Write and publish an article'
           ]);
           //確認response物件的status code是否為200
           $response->assertStatus(200);
           //確認task 資料有沒有一筆以上
           $this->assertTrue(count(Task::all()) > 1);
       }
   
       public function test_that_a_task_can_be_deleted()
       {
           //使用withoutExceptionHandling()時，會顯示所有錯誤訊息
           $this->withoutExceptionHandling();
           //建立5筆task資料
           $task = Task::factory()->times(5)->create();
           //隨機產生
           $id_to_be_deleted = random_int(1, 5);
           $this->delete("/task/$id_to_be_deleted/"); // send a request to delete it
           $this->assertDatabaseMissing('task', ['id' => $id_to_be_deleted]); // assert that the task deleted previouly does not exist in the database anymore.
       }
   
       public function test_that_a_task_can_be_completed(){
           $this->withoutExceptionHandling();
           $task_id = Task::create([
                 'name' => 'Example Name',
                 'description' => 'Demo dscription'
           ])->id; // create a task and store the id in the $task_id variable
           $response = $this->patch("/task/$task_id/complete"); //sends a patch request in order to complete the created task
           $this->assertTrue(Task::findOrFail($task_id)->is_completed() == 1); // assert that the task is now marked as completed in the database
           $response->assertJson([
                     'message' => 'task successfully marked as updated'
           ], true); // ensure that the JSON response recieved contains the message specified
           $response->assertStatus(200); // furthe ensures that a 200 response code is recieved from the patch request
         }
   }
   ```
   
   先執行`withoutExceptionHandling()`，會列出所有PHPUnit會拋錯的訊息，以方便追蹤程式，在建立Task的資料，拿id去設定route後執行，這時會回傳response物件，在用id去查詢，這筆資料的completed欄位，資料是否為1，再把response物件，確認json格式資料是不是key=message，value=task successfully marked as updated，之後在確認status code是否為200

DataBase Test適合

#### Mock

是偽造的物件來替換要執行的function，我們每個程式都去呼叫各種其他程式，但我們在編寫時，又不希望隨意呼叫，這時我們會用到Mock，不管有沒有通過測試，並不會影響到要測試的目標。

- ##### 原理
  
  Mock是監聽測試function事件，建立Mock物件，是在測試檔案建立新的物件，去執行Mock物件的測試function，所以在自己的測試目錄執行，並不會影響其他檔案。
  
  這裡注意的是Mock，是繼承的Service的物件，所以要在寫指向Service的function才會被執行

- Function Test(功能測試)
  
  大部分的測試，都算是功能測試，例如我們在測試程式要使用request，用 Mock 來做替身並且改寫，在Mockery的library的MockeryTestCaseSetUp，用Mock 物件時，會使用setUp()和tearDown()
  
  ```php
  namespace Mockery\Adapter\Phpunit;
  
  trait MockeryTestCaseSetUp
  {
      protected function setUp(): void
      {
          parent::setUp();
          $this->mockeryTestSetUp();
      }
  
      protected function tearDown(): void
      {
          $this->mockeryTestTearDown();
          parent::tearDown();
      }
  }
  ```

- ##### spy
  
  在寫mock的時候，會看到spy，它與mock有什麼差異?
  
  spy跟mock很像，在測試程式裡，mock需要建立一個相同物件，來驗證呼叫的funtion是否一樣，但是spy是不用呼叫function，直接執行function後，來驗證某個function有沒有被spy使用
  
  spy的目的是驗證呼叫的function，有沒有真的被執行，mock則是模擬的資料，是否跟預期的資料符合。

#### 流程圖

![Mock](C:\xampp\htdocs\markdown_note\assets\images\Mock.png)

![Mock-1](C:\xampp\htdocs\markdown_note\assets\images\Mock-1.png)

我們寫function測試，一般我們會在TestCase寫成繼承，讓每個測試檔案都可以直接用instance(class)，執行Mock。

```php
class TestCase extends BaseTestCase
{
    use CreatesApplication;

     /**
     * 初始化mock物件
     *
     * @param string $class
     * @return Mockery
     */
    protected function initMock($class)
    {
        $mock = \Mockery::mock($class);
        $this->app->instance($class, $mock);

        return $mock;
    }
}
```

以下示範，我們用訂單來開發票，使用OrderServiceTest的InvoiceService newInvoice()，但是我們不想要真的開發票，要用建立Moke物件，幫我們模擬開發票

```php
<?php

class OrderServiceTest extends TestCase
{
     public function testNewOrder()
    {
        $invoiceReturn = [
            'invoice_number' = '001'
        ];

        $data = [
            "invoice1", "invoice2"
        ];

        $mock = $this->initMock(InvoiceService::class);
        $mock->shouldReceive('newInvoice')
            ->once()
            ->with($data)
            ->andReturn($invoiceReturn);

        $test = $mock->newInvoice($data);
    }
}
```

以下介紹使用的Mock functuion

- shouldReceive：指定參數內的function，這裡指定InvoiceService的newInvoice()。
- once：只呼叫一次，如果失敗會顯示InvalidCountException。
- with：使用參數。
- andReturn：設定假的回傳值，使用andReturn的目的，是為了要確認shouldReceive指定function能不能值直行。

用到Mock做測試，通常是遇到某些原因不想要影響到資料，例如發mail、用API需要驗證、新增資料等等，只要會是執行後，會有風險的情況，都適合用Mock來幫你做。

##### 用Mock建立OrderServiceTest class跟沒有用Mock建立的class有什麼差異?

這裡用Mock建立OrderServiceTest class是模擬OrderServiceTest class測試newInvoice()，不是只OrderServiceTest 物件，如果沒有用Mock就是用OrderServiceTest執行newInvoice()

##### 用Mock建立OrderServiceTest class，確認這個物件的內容是什麼樣子，跟沒使用Mock有什麼差異?

Mock是代替OrderServiceTest class，物件中會有OrderServiceTest的function、service、interface等等，必須要用shouldReceive去呼叫function來模擬，沒使用Mock等於是直接用OrderServiceTest class，去實做function、service、interface等等

Mock是來自[mockery](https://github.com/mockery/mockery)這個套件，可以參考laravel的Mockery\Adapter\Phpunit\MockeryTestCase

##### 範例的initMock是模擬InvoiceService class，用shouldReceive('newInvoice')跟直接執行newInvoice()，這兩行是什麼意思?

shouldReceive('newInvoice')是模擬InvoiceService class，呼叫newInvoice()，這個時候Mock沒有開始執行

##### Phalcon mock範例

```php
protected function setUp() {
    $mock = $this->getMock('\\Phalcon\\Http\\Request', ['get']);
    $mock->expects($this->any())
        ->method('get')
        ->will($this->returnValue('Succeeded!'));
    $this->di->setShared('request', $mock);
}
```

`setup()`將Request物件mock起來，使用`get()`，Succeeded用字串方式，當參數使用，只用一次

將Phalcon DI類別的request，設定跟$mock物件相同

```php
public function test1TestCase() {
    $response = $this->dispatch('/index');
    $this->assertEquals('Succeeded!', $response);
}

public function test2TestCase() {
    $response = $this->dispatch('/index');
    $this->assertEquals('Succeeded!', $response);
}
```

`test1TestCase()`執行route index，回傳response，確認是否為Succeeded!

`test2TestCase()`執行route index，回傳response，確認是否為Succeeded!

https://phpunit.readthedocs.io/en/9.5/test-doubles.html?highlight=Mock#mock-objects

https://www.cnblogs.com/bourneli/archive/2012/06/29/2570440.html

https://www.cnblogs.com/cjjjj/p/10623534.html

#### 問題

- Mock 的用途：可以替我們執行function，但不是真的在執行

- Mock做了哪些事：將我們要測試的function，包裝成Mock之後，在執行測試

- 為什麼Mock可以代替 ：Mock是繼承service的物件，所以可以代替測試的fcunction，也並不會影響資料被改變

- Mock產生出來的結果是什麼：會建立測試function的Mock物件，去模擬執行測試function

- 跑測試顯示錯誤
  
  PHPUnit 9.5.0 by Sebastian Bergmann and contributors.
  
  Warning:       Your XML configuration validates against a deprecated schema.
  
  Suggestion:    Migrate your XML configuration using "--migrate-configuration"!

#### 錯誤

- 如果只建立Mock物件，沒指向function會顯示錯誤：
   Method newInvoice(....) from Mockery_0_App_Services_InvoiceService should be called exactly 1 times but called 0 times.
  
  代表建立一個Mock物件，執行0次，以上面的範例少寫$test = $mock->newInvoice()，會顯示錯誤。

參考資料：

- https://dustinhsiao21.github.io/laravel/use-mock-in-laravel-phpunit/
- https://learnku.com/docs/laravel/5.8/mocking/3941
- https://laravel.com/docs/8.x/mocking
- https://ithelp.ithome.com.tw/articles/10217378
- https://blog.miniasp.com/post/2019/02/18/Unit-testing-Integration-testing-e2e-testing
- https://stackoverflow.com/questions/60837159/laravel-dusk-teardown-must-be-compatible-with-illuminate-foundation-testing-te
- https://blog.hinablue.me/phalconphp-functional-test-yu-phpunit-mock-ai-hen-qing-chou/
- https://blog.givemin5.com/tdd-1-unit-test-3a-yuan-ze/
- https://github.com/recca0120/fight-editor
- https://www.jyt0532.com/2018/01/04/test-double-spy/
