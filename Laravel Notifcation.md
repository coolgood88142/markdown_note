## Laravel Notification

除了使用Email發送，laravel可以用database、推播、SMS 、Slack做通知。

laravel是用Notifiable做發送，發送方式可分兩種

### Notifiable Trait

發送通知用Notifiable Trait，將每個通知都用一個class做通知，建立一個Notifications的class，用`notify()`或`facade()`

- notify：執行用model去接收通知，例如：user model接收`InvoicePaid()`
  
  ```php
  class User extends Authenticatable
  {
      use Notifiable;
  }
  ```
  
  ```php
  $user->notify(new InvoicePaid($invoice));
  ```

- facade：用send做發送，例如：user model與`InvoicePaid()`發送
  
  ```php
  Notification::send($users, new InvoicePaid($invoice));
  ```

#### 指定傳出管道

每個通知有`via()`可以決定使用哪個傳出管道`mail`、`database`、`broadcast`、`nexmo`、`slack`

```php
public function via($notifiable)
{
    return $notifiable->prefers_sms ? ['nexmo'] : ['mail', 'database'];
}
```

#### 排隊通知

要用ShouldQueue和Queueable，當系統偵測到有ShouldQueue會自動排隊做通知

```php
class InvoicePaid extends Notification implements ShouldQueue
{
    use Queueable;

    // ...
}
```

如果想要用延遲通知，要使用`delay`，例如：設定10分鐘後在傳送

```php
$when = Carbon::now()->addMinutes(10);

$user->notify((new InvoicePaid($invoice))->delay($when));
```

- **notify**
  
  notify是用接收notification的method
  
  ```php
  use App\Notifications\InvoicePaid;
  
  $user->notify(new InvoicePaid($invoice));
  ```

- **facade**
  
  要用send
  
  ```php
  Notification::send($users, new InvoicePaid($invoice));
  ```

#### EMail

使用Email做發送訊息

```php
public function toMail($notifiable)
{
    $url = url('/invoice/'.$this->invoice->id);

    return (new MailMessage)
                ->greeting('Hello!')
                ->line('One of your invoices has been paid!')
                ->action('View Invoice', $url)
                ->line('Thank you for using our application!');
}
```

在寄信之前要在config/app.php，設定name(要確認為什麼官網要這樣設定)

#### Markdown Mail

用Markdown 模板建立通知的樣式，內容有點像html

```bash
php artisan make:notification InvoicePaid --markdown=mail.invoice.paid
```

在notification的toMail改成下面的程式

```php
public function toMail($notifiable)
{
    $url = url('/invoice/'.$this->invoice->id);

    return (new MailMessage)
                ->subject('Invoice Paid')
                ->markdown('mail.invoice.paid', ['url' => $url]);
}
```

Database

Broadcast

SMS

Slack

參考資料：

- https://ithelp.ithome.com.tw/articles/10231475?sc=rss.qu
- https://laravel.com/docs/6.x/notifications
- https://www.youtube.com/watch?v=Uj0jIOHvNYE&t=50s
- https://www.itread01.com/content/1544922730.html
- https://tn710617.github.io/zh-tw/laravel-digging-deeper-notifications/
- https://www.twblogs.net/a/5cc07ef7bd9eee3aed78659f
- https://www.programmersought.com/article/44691291904/
- http://yhhuang1966.blogspot.com/2017/11/xampp.html