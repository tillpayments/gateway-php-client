# PaymentGateway Client

## Preparation of Repository

Replace `{HOSTNAME}` occurrences to your dedicated host name in the following files:

- `src/Client.php`
- `src/Xml/Generator.php`

Available HOSTNAMEs:
- SANDBOX/TEST ENV = test-gateway.tillpayments.com
- PRODUCTION ENV = gateway.tillpayments.com

## Usage:

```php
<?php

use PaymentGateway\Client\Client;
use PaymentGateway\Client\Data\Customer;
use PaymentGateway\Client\Transaction\Debit;
use PaymentGateway\Client\Transaction\Result;

// Include the autoloader (if not already done via Composer autoloader)
require_once('path/to/initClientAutoload.php');

// Instantiate the "PaymentGateway\Client\Client" with your credentials
$client = new Client("username", "password", "apiKey", "sharedSecret");

$customer = new Customer();
$customer->setBillingCountry("AU")
	->setEmail("customer@email.test");

$debit = new Debit();

// define your transaction ID: e.g. 'myId-'.date('Y-m-d').'-'.uniqid()
$merchantTransactionId = 'your_transaction_id'; // must be unique

$debit->setTransactionId($merchantTransactionId)
	->setSuccessUrl($redirectUrl)
	->setCancelUrl($redirectUrl)
	->setCallbackUrl($callbackUrl)
	->setAmount(10.00)
	->setCurrency('AUD')
	->setCustomer($customer);

// send the transaction
$result = $client->debit($debit);

// now handle the result
if ($result->isSuccess()) {
	//act depending on $result->getReturnType()

    $gatewayReferenceId = $result->getReferenceId(); //store it in your database

    if ($result->getReturnType() == Result::RETURN_TYPE_ERROR) {
        //error handling
        $errors = $result->getErrors();
        //cancelCart();

    } elseif ($result->getReturnType() == Result::RETURN_TYPE_REDIRECT) {
        //redirect the user
        header('Location: '.$result->getRedirectUrl());
        die;

    } elseif ($result->getReturnType() == Result::RETURN_TYPE_PENDING) {
        //payment is pending, wait for callback to complete

        //setCartToPending();

    } elseif ($result->getReturnType() == Result::RETURN_TYPE_FINISHED) {
        //payment is finished, update your cart/payment transaction

        //finishCart();
    }
}

## License

The MIT License (MIT). Please see [LICENSE](LICENSE) for more information.
```
