<p align="center"><img src="resources/images/payment.png?raw=true"></p>

# پکیج درگاه پرداخت برای لاراول

[![Software License][ico-license]](LICENSE.md)
[![Latest Version on Packagist][ico-version]][link-packagist]
[![Total Downloads on Packagist][ico-download]][link-packagist]
[![StyleCI](https://github.styleci.io/repos/169948762/shield?branch=master)](https://github.styleci.io/repos/169948762)
[![Maintainability](https://api.codeclimate.com/v1/badges/e6a80b17298cb4fcb56d/maintainability)](https://codeclimate.com/github/shetabit/payment/maintainability)
[![Quality Score][ico-code-quality]][link-code-quality]

این پکیج برای پرداخت انلاین توسط درگاه های ملختف در لاراول ایجاد شده است.

> این پکیج با درگاه های پرداخت مختلفی کار میکنه. در صورتی که درگاه مورد نظرتون رو در لیست درایورهای موجود پیدا نکردید میتونید برای درگاهی که استفاده میکنید درایور مورد نظرتون رو بسازید.

- [داکیومنت فارسی][link-fa]
- [english documents][link-en]

# لیست محتوا

- [درایور های موجود](#درایورهای-موجود)
- [نصب](#نصب)
- [تنظیمات](#تنظیمات)
- [طریقه استفاده](#طریقه-استفاده)
  - [کار با صورتحساب ها](#کار-با-صورتحساب-ها)
  - [ثبت درخواست برای پرداخت صورتحساب](#ثبت-درخواست-برای-پرداخت-صورتحساب)
  - [پرداخت صورتحساب](#پرداخت-صورتحساب)
  - [اعتبار سنجی پرداخت](#اعتبار-سنجی-پرداخت)
  - [ایجاد درایور دلخواه](#ایجاد-درایور-دلخواه)
  - [متدهای سودمند](#متدهای-سودمند)
- [تغییرات](#تغییرات)
- [مشارکت کننده ها](#مشارکت-کننده-ها)
- [امنیت](#امنیت)
- [توسعه دهندگان](#توسعه-دهندگان)
- [لایسنس](#لایسنس)

# درایورهای موجود

- [زرین پال](https://www.zarinpal.com)
- [ایران کیش](http://irankish.com)
- )[بانک سامان](https://www.sep.ir)
- درایورهای دیگر ساخته خواهند شد یا اینکه بسازید و درخواست `merge` بدید.

> در صورتی که درایور مورد نظرتون موجود نیست, میتونید برای درگاه پرداخت موردنظرتون درایور بسازید.

## نصب

نصب با استفاده از کامپوزر

``` bash
$ composer require shetabit/payment
```

## تنظیمات

درصورتی که از `Laravel 5.5` یا ورژن های بالاتر استفاده میکنید نیازی به انجام تنظیمات `providers` و `alias` نخواهید داشت.

درون فایل `config/app.php` دستورات زیر را وارد کنید

```php
# In your providers array.
'providers' => [
    ...
    Shetabit\Payment\Provider\PaymentServiceProvider::class,
],

# In your aliases array.
'aliases' => [
    ...
    'Payment' => Shetabit\Payment\Facade\Payment::class,
],
```

سپس دستور `php artisan vendor:publish` را اجرا کنید تا فایل `config/payment.php` درون دایرکتوری تنظیمات لاراول قرار بگیرد.

درون فایل تنظیمات در قسمت `default driver` میتوانید درایوری که قصد استفاده از ان را دارید قرار دهید تا تمامی پرداخت ها از آن طریق انجام شود.

```php
// Eg. if you want to use zarinpal.
'default' => 'zarinpal',
```

سپس تنظیمات مرتبط با درایوری که قصد استفاده از ان را دارید انجام دهید

```php
'drivers' => [
    'zarinpal' => [
        // Fill all the credentials here.
        'apiPurchaseUrl' => 'https://www.zarinpal.com/pg/rest/WebGate/PaymentRequest.json',
        'apiPaymentUrl' => 'https://www.zarinpal.com/pg/StartPay/',
        'apiVerificationUrl' => 'https://www.zarinpal.com/pg/rest/WebGate/PaymentVerification.json',
        'merchantId' => '',
        'callbackUrl' => 'http://yoursite.com/path/to',
        'description' => 'payment in '.config('app.name'),
    ],
    ...
]
```

## طریقه استفاده

در تمامی پرداخت ها اطلاعات پرداخت درون صورتحساب شما نگهداری میشود. برای استفاده از پکیج ابتدا نحوه ی استفاده از کلاس `Invoice` به منظور کار با صورتحساب ها را توضیح میدهیم.

#### کار با صورتحساب ها

قبل از انجام هرکاری نیاز به ایجاد یک صورتحساب دارید. برای ایجاد صورتحساب میتوانید از کلاس `Invoice` استفاده کنید.

درون کد خودتون به شکل زیر عمل کنید:

```php
# On the top of the file.
use Shetabit\Payment\Invoice;
...

# create new invoice
$invoice = new Invoice;

# set invoice amount
$invoice->amount(1000);

# add invoice details : we have 4 syntax
// 1
$invoice->detail(['detailName' => 'your detail goes here']);
// 2 
$invoice->detail('detailName','your detail goes here');
// 3
$invoice->detail(['name1' => 'detail1','name2' => 'detail2']);
// 4
$invoice->detail('detailName1','your detail1 goes here')
        ->detail('detailName2','your detail2 goes here');

```

متدهای موجود برای کار با صورتحساب ها:

- `uuid` : یک ایدی یونیک برای صورتحساب تنظیم میکند
- `getUuid` : ایدی یونیک صورتحساب را برمیگرداند
- `detail` : توضیحات یا مواردی که مرتبط به صورتحساب است را به صورتحساب اظافه میکند
- `getDetails` : تمامی موارد مرتبطی که به صورتحساب افزوده شده است را برمیگرداند
- `amount` : مقدار هزینه ای که باید پرداخت شود را مشخص میکند
- `getAmount` : هزینه ی صورتحساب را برمیگرداند
- `transactionId` : شماره تراکنش صورتحساب را مشخص میکند
- `getTransactionId` : شماره تراکنش صورتحساب را برمیگرداند
- `via` : درایوری که قصد پرداخت صورتحساب با آن را داریم مشخص میکند
- `getDriver` : درایور انتخاب شده را برمیگرداند

#### ثبت درخواست برای پرداخت صورتحساب
به منظور پرداخت تمامی صورتحساب ها به یک شماره تراکنش بانکی یا `transactionId` نیاز خواهیم داشت.
با ثبت درخواست به منظور پرداخت میتوان شماره تراکنش بانکی را دریافت کرد:

```php
# On the top of the file.
use Shetabit\Payment\Invoice;
use Shetabit\Payment\Facade\Payment;
...

# create new invoice
$invoice = (new Invoice)->amount(1000);

# purchase the given invoice
Payment::purchase($invoice,function($driver, $transactionId) {
	// we can store $transactionId in database
});

# purchase method accepts a callback function
Payment::purchase($invoice, function($driver, $transactionId) {
    // we can store $transactionId in database
});
```

#### پرداخت صورتحساب

با استفاده از شماره تراکنش یا `transactionId` میتوانیم کاربر را به صفحه ی پرداخت بانک هدایت کنیم:

```php
# On the top of the file.
use Shetabit\Payment\Invoice;
use Shetabit\Payment\Facade\Payment;
...

# create new invoice
$invoice = (new Invoice)->amount(1000);
# purchase and pay the given invoice
// you should use return statement to redirect user to the bank's page.
return Payment::purchase($invoice, function($driver, $transactionId) {
    // store transactionId in database, we need it to verify payment in future.
})->pay();

# do all things together a single line
return Payment::purchase(
    (new Invoice)->amount(1000), 
    function($driver, $transactionId) {
    	// store transactionId in database.
        // we need the transactionId to verify payment in future
	}
)->pay();
```

#### اعتبار سنجی پرداخت

بعد از پرداخت شدن صورتحساب توسط کاربر, بانک کاربر را به یکی از صفحات سایت ما برمیگردونه و ما با اعتبار سنجی میتونیم متوجه بشیم کاربر پرداخت رو انجام داده یا نه!

```php
# On the top of the file.
use Shetabit\Payment\Facade\Payment;
use Shetabit\Payment\Exceptions\InvalidPaymentException;
...

# you need to verify the payment to insure the invoice has been paid successfully
// we use transaction's id to verify payments
// its a good practice to add invoice's amount.
try {
	Payment::amount(1000)->transactionId($transaction_id)->verify();
    ...
} catch (InvalidPaymentException $exception) {
    /**
    	when payment is not verified , it throw an exception.
    	we can catch the excetion to handle invalid payments.
    	getMessage method, returns a suitable message that can be used in user interface.
    **/
    echo $exception->getMessage();
}
```

در صورتی که پرداخت توسط کاربر به درستی انجام نشده باشه یک استثنا از نوع `InvalidPaymentException` ایجاد میشود که حاوی پیام متناسب با پرداخت انجام شده است.

#### ایجاد درایور دلخواه:

 برای ایجاد درایور جدید ابتدا نام (اسم) درایوری که قراره بسازید رو به لیست درایور ها اظافه کنید و لیست تنظیات مورد نیاز را نیز مشخص کنید.

```php
'drivers' => [
    'zarinpal' => [...],
    'my_driver' => [
        ... # Your Config Params here.
    ]
]
```

کلاس درایوری که قصد ساختنش رو دارید باید کلاس `Shetabit\Payment\Abstracts\Driver` رو به ارث ببره.

به عنوان مثال:

```php
namespace App\Packages\PaymentDriver;

use Shetabit\Payment\Invoice;
use Shetabit\Payment\Abstracts\Driver;
use Shetabit\Payment\Exceptions\InvalidPaymentException;

class MyDriver extends Driver
{
    protected $invoice; // invoice

    protected $settings; // driver settings

    public function __construct(Invoice $invoice, $settings)
    {
        $this->invoice($invoice); // set the invoice
        $this->settings = (object) $settings; // set settings
    }

    // purchase the invoice, save its transactionId and finaly return it
    public function purchase() {
        // request for a payment transaction id
        ...
            
        $this->invoice->transactionId($transId);
        
        return $transId;
    }
    
    // redirect into bank using transactionId, to complete the payment
    public function pay() {
        // its better to set bankApiUrl in config/payment.php and retrieve it here:
        $bankUrl = $this->settings->bankApiUrl; // bankApiUrl is the config name.

        //prepare payment url
        $payUrl = $bankUrl.$this->invoice->getTransactionId();

        // redirect to the bank
        return redirect()->to($payUrl);
    }
    
    // verify the payment (we must verify to insure that user has paid the invoice)
    public function verify() {
        $verifyPayment = $this->settings->verifyApiUrl;
        
        $verifyUrl = $verifyPayment.$this->invoice->getTransactionId();
        
        ...
        
        /**
			then we send a request to $verifyUrl and if payment is not
			we throw an InvalidPaymentException with a suitable
        **/
        throw new InvalidPaymentException('a suitable message');
    }
}
```

بعد از اینکه کلاس درایور خودتون رو ایجاد کردید به فایل `Config/payment.php` برید و درایور خودتون رو در قسمت `map` اظافه کنید.

```php
'map' => [
    ...
    'my_driver' => App\Packages\PaymentDriver\MyDriver::class,
]
```

**نکته:** دقت کنید کلیدی که قسمت `map` قرار میدهید باید همنام با نامی باشد که در قسمت `drivers` قرار داده اید.

#### متدهای سودمند

- `callbackUrl` : با استفاده از این متد به صورت داینامیک میتوانید ادرس صفحه ای که بعد از پرداخت انلاین کاربر به ان هدایت میشود را مشخص کنید

```php
  # On the top of the file.
  use Shetabit\Payment\Invoice;
  use Shetabit\Payment\Facade\Payment;
  ...
  
  # create new invoice
  $invoice = (new Invoice)->amount(1000);
  
  # purchase the given invoice
  Payment::callbackUrl($url)->purchase(
      $invoice, 
      function($driver, $transactionId) {
      // we can store $transactionId in database
  	}
  );
```

- `amount`: به کمک این متد میتوانید به صورت مستقیم هزینه صورتحساب را مشخص کنید

```php
  # On the top of the file.
  use Shetabit\Payment\Invoice;
  use Shetabit\Payment\Facade\Payment;
  ...
  
  # purchase (we set invoice to null)
  Payment::callbackUrl($url)->amount(1000)->purchase(
      null, 
      function($driver, $transactionId) {
      // we can store $transactionId in database
  	}
  );
```

- `via` : به منظور تغییر درایور در هنگام اجرای برنامه مورد استفاده قرار میگیرد

```php
  # On the top of the file.
  use Shetabit\Payment\Invoice;
  use Shetabit\Payment\Facade\Payment;
  ...
  
  # create new invoice
  $invoice = (new Invoice)->amount(1000);
  
  # purchase the given invoice
  Payment::via('driverName')->purchase(
      $invoice, 
      function($driver, $transactionId) {
      // we can store $transactionId in database
  	}
  );
```

## تغییرات

برای مشاهده آخرین تغییرات انجام شده در پکیج [قسمت تغییرات](CHANGELOG.md) را بررسی کنید.

## مشارکت کننده ها

برای مشاهده لیست مشارکت کننده ها [CONTRIBUTING](CONTRIBUTING.md) and [CONDUCT](CONDUCT.md) را بررسی کنید.

## امنیت

در صورتی که مشکل امنیتی در پکیج پیدا کردید به منظور رفع مشکل با ایمیل khanzadimahdi@gmail.com در ارتباط باشید.

## توسعه دهندگان

- [Mahdi khanzadi][link-author]
- [All Contributors][link-contributors]

## لایسنس

توسعه و تولید تحت لایسنس MIT است. برای اطلاعات بیشتر [فایل لایسنس](LICENSE.md) را مطالعه کنید.

[ico-version]: https://img.shields.io/packagist/v/shetabit/payment.svg?style=flat-square
[ico-download]: https://img.shields.io/packagist/dt/shetabit/payment.svg?color=%23F18&style=flat-square
[ico-license]: https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square
[ico-code-quality]: https://img.shields.io/scrutinizer/g/shetabit/payment.svg?label=Code%20Quality&style=flat-square

[link-fa]: README-FA.md
[link-en]: README.md
[link-packagist]: https://packagist.org/packages/shetabit/payment
[link-code-quality]: https://scrutinizer-ci.com/g/shetabit/payment
[link-author]: https://github.com/khanzadimahdi
[link-contributors]: ../../contributors
