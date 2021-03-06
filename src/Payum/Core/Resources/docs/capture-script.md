# Capture script.

## Simple use case

To perform a capture you just have to do:

```php
<?php
use Payum\Core\Request\Capture;

$gateway->execute(new Capture($order));

// or

$gateway->execute(new Capture($details));
```

_**Note**: If you've got the "RequestNotSupported" it either means Payum or a gateway do not support the capture._

## Advanced (Secure) use case

To use that you have to configure token factory and create a capture script:

```php
<?php
$token = $tokenFactory->createCaptureToken($gatewayName, $details, 'afterCaptureUrl');

header("Location: ".$token->getTargetUrl());
```

This is the script which does all the job related to capturing payments. 
It may show a credit card form, an iframe or redirect a user to gateway side. 
The action provides some basic security features. 
Each capture url is completely unique for each purchase, and once we done the url is invalidated.
After a user will be redirected to after url, in our case it will be `done.php` script. 
Here's an example of [done.php](done-script.md) script:

```php
<?php
//capture.php

use Payum\Core\Request\Capture;
use Payum\Core\Reply\HttpRedirect;

include 'config.php';

$token = $payum->getRequestVerifier()->verify($_REQUEST);
$gateway = $payum->getGateway($token->getGatewayName());

if ($reply = $gateway->execute(new Capture($token), true)) {
    if ($reply instanceof HttpRedirect) {
        header("Location: ".$reply->getUrl());
        die();
    }

    throw new \LogicException('Unsupported reply', null, $reply);
}

$payum->getRequestVerifier()->invalidate($token);

header("Location: ".$token->getAfterUrl());
```

_**Note**: If you've got the "Unsupported reply" you have to add an if condition for that. There we have to convert the reply to http response._

Back to [index](index.md).
