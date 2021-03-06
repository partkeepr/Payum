# Get it started.

In this chapter we are going to talk about the most common task: purchasing a product.
We assume you already read [payum's get it started documentation](https://github.com/Payum/Core/blob/master/Resources/docs/get-it-started.md).

## Installation

The preferred way to install the library is using [composer](http://getcomposer.org/).
Run composer require to add dependencies to _composer.json_:

```bash
php composer.phar require "payum/paypal-rest"
```

## Configuration

```php
<?php
//config.php

use Payum\Core\PayumBuilder;
use Payum\Core\Payum;

/** @var Payum $payum */
$payum = (new PayumBuilder())
    ->addDefaultStorages()
    ->addGateway('gatewayName', [
        'factory' => 'paypal_rest'
        'client_id' => 'REPLACE IT',
        'client_secret' => 'REPLACE IT',
        'config_path' => 'REPLACE IT',
    ])

    ->getPayum()
;
```

## Prepare payment

```php
<?php
// prepare.php

include 'config.php';

use PayPal\Api\Amount;
use PayPal\Api\Payer;
use PayPal\Api\RedirectUrls;
use PayPal\Api\Transaction;

$storage = $payum->getStorage($paypalRestPaymentDetailsClass);

$payment = $storage->create();
$storage->update($payment);

$payer = new Payer();
$payer->payment_method = "paypal";

$amount = new Amount();
$amount->currency = "USD";
$amount->total = "1.00";

$transaction = new Transaction();
$transaction->amount = $amount;
$transaction->description = "This is the payment description.";

$captureToken = $payum->getTokenFactory()->createCaptureToken('paypalRest', $payment, 'create_recurring_payment.php');

$redirectUrls = new RedirectUrls();
$redirectUrls->return_url = $captureToken->getTargetUrl();
$redirectUrls->cancel_url = $captureToken->getTargetUrl();

$payment->intent = "sale";
$payment->payer = $payer;
$payment->redirect_urls = $redirectUrls;
$payment->transactions = array($transaction);

$storage->update($payment);

header("Location: ".$captureToken->getTargetUrl());
```

That's it. As you see we configured Paypal Rest `config.php` and set details `prepare.php`.
[capture.php](https://github.com/Payum/Payum/blob/master/src/Payum/Core/Resources/docs/capture-script.md) and [done.php](https://github.com/Payum/Payum/blob/master/src/Payum/Core/Resources/docs/done-script.md) scripts remain same.
Here you have to modify a `gatewayName` value. Set it to `paypal_pro_checkout`. The rest remain the same as described basic [get it started](https://github.com/Payum/Core/blob/master/Resources/docs/get-it-started.md) documentation.

## Next

* [Core's Get it started](https://github.com/Payum/Core/blob/master/Resources/docs/get-it-started.md).
* [The architecture](https://github.com/Payum/Core/blob/master/Resources/docs/the-architecture.md).
* [Supported gateways](https://github.com/Payum/Core/blob/master/Resources/docs/supported-gateways.md).
* [Storages](https://github.com/Payum/Core/blob/master/Resources/docs/storages.md).
* [Capture script](https://github.com/Payum/Core/blob/master/Resources/docs/capture-script.md).
* [Authorize script](https://github.com/Payum/Core/blob/master/Resources/docs/authorize-script.md).
* [Done script](https://github.com/Payum/Core/blob/master/Resources/docs/done-script.md).

Back to [index](index.md).
