# Africa's Talking PHP SDK

> The wrapper provides convenient access to the Africa's Talking API from applications written in PHP.

## Documentation
Take a look at the [API docs here](http://docs.africastalking.com).

## Install 

You can install the PHP SDK via composer or by downloading the source

#### Via Composer

The recommended way to install the SDK is with [Composer](http://getcomposer.org/). 
```bash
$ composer require africastalking/africastalking
```

## Usage

The SDK needs to be instantiated using your username and API key, which you can get from the [dashboard](https://account/africastalking.com).

> You can use this SDK for either production or sandbox apps. For sandbox, the app username is **ALWAYS** `sandbox`

```php
<?php

    use AfricasTalking\SDK\AfricasTalking;

    $username = 'YOUR_USERNAME'; // use 'sandbox' for development in the test environment
    $apiKey 	= 'YOUR_API_KEY'; // use your sandbox app API key for development in the test environment
    $AT = new AfricasTalking($username, $apiKey);

```
You can now make API calls using the `$AT` object we have just created.  See [example](example/) for more usage examples.

### Airtime

Send airtime to phone numbers

```php
$airtime = $AT->airtime();
$airtime->send($options);
```
 The options array `$options` must contain the following keys:

- `recipients`: Contains an array of arrays containing the following keys
    - `phoneNumber`: Recipient of airtime
    - `amount`: Amount sent `>= 10 && <= 10K` with currency e.g `KES 100`


### SMS

```php
$SMS = $AT->sms();
```

#### Send SMS

```php
$SMS->send($options);
```

The options array `$options` has the following keys:

- `message`: SMS content. `REQUIRED`
- `to`: A single recipient or an array of recipients. `REQUIRED`
    - array of recipients contains 
- `from`: Shortcode or alphanumeric ID that is registered with Africa's Talking account.
- `enqueue`: Set to `true` if you would like to deliver as many messages to the API without waiting for an acknowledgement from telcos.

#### Send Premium SMS

```php
$SMS->sendPremium($options);
```
The options array `$options` has the following keys:
- `message`: SMS content. `REQUIRED`
- `to`: A single recipient or an array of recipients. `REQUIRED`
    - array of recipients contains 
- `from`: Shortcode or alphanumeric ID that is registered with Africa's Talking account.
- `keyword`: Value is a premium keyword `REQUIRED`
- `linkId`: "This is used for premium services to send OnDemand messages. We forward the linkId to your application when the user sends a message to your service. `REQUIRED`
- `retryDurationInHours`: "It specifies the number of hours your subscription message should be retried in case it's not delivered to the subscriber"

#### Fetch Messsages

```php
$SMS->fetchMessage()
```

#### Create subscription

Create a premium subscription

```php
$SMS->createSubscription($options);
```
The options array `$options` has the following keys
- `shortCode`: This is a premium short code mapped to your account. `REQUIRED`
- `keyword`: Value is a premium keyword under the above short code and mapped to your account. `REQUIRED`
- `phoneNumber`: The phoneNumber to be subscribed `REQUIRED`
- `checkoutToken`: This is a token used to validate the subscription request `REQUIRED`

#### Fetch Subscription

```php
$SMS->fetchSubscription($options);
```
The options array `$options` has the following keys
- `shortCode`: This is a premium short code mapped to your account. `REQUIRED`
- `keyword`: Value is a premium keyword under the above short code and mapped to your account. `REQUIRED`
- `lastReceivedId`: ID of the subscription you believe to be your last. Defaults to `0`


### Payment

```php
$payments = $AT->payments();
```

#### Credit card checkout

Initiate a card checkout

```php
$payments->cardCheckout($options);
```
The options array `$options` has the following keys
- `productName`: Payment Product as setup on your account. `REQUIRED`
- `checkoutToken`: Token that has been generated by our APIs as as result of charging a user's Payment Card in a previous transaction. When using a token, the `paymentCard` data should NOT be populated.
- `paymentCard`: Payment Card to be charged:

    - `number`: The payment card number. `REQUIRED`
    - `cvvNumber`: The 3 or 4 digit Card Verification Value. `REQUIRED`
    - `expiryMonth`: The expiration month on the card (e.g `8`) `REQUIRED`
    - `authToken`: The card's ATM PIN. `REQUIRED`
    - `countryCode`: The 2-Digit countryCode where the card was issued (only `NG` is supported). `REQUIRED`

- `currencyCode`: 3-digit ISO format currency code (only `NGN` is supported). `REQUIRED`
- `amount`: Payment amount. `REQUIRED`
- `narration`: A short description of the transaction `REQUIRED`
- `metadata`: Some optional data to associate with transaction.

#### Validate credit card checkout

```php
$payments->validateCardCheckout($options);
```
The options array `$options` has the following keys
- `transactionId`: The transaction that your application wants to validate. `REQUIRED`
- `otp`: One Time Password that the card issuer sent to the client. `REQUIRED`

##### Bank checkout

```php
$payments->bankCheckout($options);
```
The options array `$options` has the following keys
- `productName`: Payment Product as setup on your account. `REQUIRED`
- `bankAccount`: Bank account to be charged:

    - `accountName`: The name of the bank account. `REQUIRED`
    - `accountNumber`: The account number. `REQUIRED`
    - `bankCode`: A 6-Digit [Integer Code](http://docs.africastalking.com/bank/checkout) for the bank that we allocate. See `payments.BANK.*` for supported banks. `REQUIRED`
    - `dateOfBirth`: Date of birth of the account owner (`YYYY-MM-DD`). Required for Zenith Bank Nigeria.

- `currencyCode`: 3-digit ISO format currency code (only `NGN` is supported). `REQUIRED`
- `amount`: Payment amount. `REQUIRED`
- `narration`: A short description of the transaction `REQUIRED`
- `metadata`: Some optional data to associate with transaction.

##### Validate bank checkout

```php
$payments->validateBankCheckout($options);
```
The options array `$options` has the following keys:
- `transactionId`: The transaction that your application wants to validate. `REQUIRED`
- `otp`: One Time Password that the bank sent to the client. `REQUIRED`

#### Bank transfer

Initiate a bank transfer

```php
$payments->bankTransfer($options);
```
The options array `$options` has the following keys:
- `productName`: Payment Product as setup on your account. `REQUIRED`
- `recipients`: A list of recipients. Each recipient has:
    - `bankAccount`: Bank account to be charged:
        - `accountName`: The name of the bank account.
        - `accountNumber`: The account number `REQUIRED`
        - `bankCode`: A 6-Digit Integer Code for the bank that we allocate; See `payments.BANK.*` for supported banks. `REQUIRED`
    - `currencyCode`: 3-digit ISO format currency code (only `NGN` is supported). `REQUIRED`
    - `amount`: Payment amount. `REQUIRED`
    - `narration`: A short description of the transaction `REQUIRED`
    - `metadata`: Some optional data to associate with transaction.


#### Mobile Checkout

Request payment from customer on mobile money

```php
$payments->mobileCheckout($options);
```
The options array `$options` has the following keys:
- `productName`: Your Payment Product. `REQUIRED`
- `phoneNumber`: The customer phone number (in international format; e.g. `25471xxxxxxx`). `REQUIRED`
- `currencyCode`: 3-digit ISO format currency code (e.g `KES`, `USD`, `UGX` etc.) `REQUIRED`
- `amount`: This is the amount. `REQUIRED`
- `metadata`: Some optional data to associate with transaction.

#### Mobile B2C

Send mobile money to customers

```php
$payments->mobileB2C($options);
```
The options array `$options` has the following keys:

- `productName`: Your Payment Product. `REQUIRED`
- `recipients`: A list of **up to 10** recipients. Each recipient has:

    - `phoneNumber`: The payee phone number (in international format; e.g. `25471xxxxxxx`). `REQUIRED`
    - `currencyCode`: 3-digit ISO format currency code (e.g `KES`, `USD`, `UGX` etc.) `REQUIRED`
    - `amount`: Payment amount. `REQUIRED`
    - `reason`: This field contains a string showing the purpose for the payment. If set, it should be one of the following
        
        ```
        payments.REASON.SALARY
        payments.REASON.SALARY_WITH_CHARGE
        payments.REASON.BUSINESS
        payments.REASON.BUSINESS_WITH_CHARGE
        payments.REASON.PROMOTION
        ```

    - `metadata`: Some optional data to associate with transaction.


#### Mobile B2B

Send mobile money payments TO businesses eg banks FROM your payment wallet

```php
$payments->mobileB2B($options);
```
The options array `$options` has the following keys:
- `productName`: Your Payment Product as setup on your account. `REQUIRED`
- `provider`: This contains the payment provider that is facilitating this transaction. Supported providers at the moment are:
    
    ```
      payments.PROVIDER.ATHENA
      payments.PROVIDER.MPESA
    ```
- `transferType`: This contains the payment provider that is facilitating this transaction. Supported providers at the moment are:
    
    ```
      payments.TRANSFER_TYPE.BUY_GOODS
      payments.TRANSFER_TYPE.PAYBILL
      payments.TRANSFER_TYPE.DISBURSE_FUNDS
      payments.TRANSFER_TYPE.B2B_TRANSFER
    ```
- `currencyCode`: 3-digit ISO format currency code (e.g `KES`, `USD`, `UGX` etc.) `REQUIRED`
- `destinationChannel`: This value contains the name or number of the channel that will receive payment by the provider. `REQUIRED`
- `destinationAccount`: This value contains the account name used by the business to receive money on the provided destinationChannel. `REQUIRED`
- `amount`: Payment amount. `REQUIRED`
- `metadata`: Some optional data to associate with transaction.


### Voice

```php
$voice = $AT->voice();
```

#### Making a call

```php
$voice->call($options)
```
The options array `$options` has the following keys:
- `to`: The phone number that you wish to dial (in international format) `REQUIRED`
- `from`: Your Africa's Talking phone number (in international format). `REQUIRED`

#### Fetch queued calls

```php
$voice->fetchQueuedCalls($phoneNumber);
```
The `$phoneNumber` is phone number mapped to your AfricasTalking account


#### Upload media file

```php
$voice->uploadMediaFile($options);
```
The options array `$options` has the following keys:
- `phoneNumber`: phone number mapped to your AfricasTalking account 
- `url`: The url of the file to upload. Don't forget to start with http://

#### MessageBuilder

Build voice xml when callback URL receives a POST from the voice API

```php
$voiceActions = $voice->messageBuilder();
```

Actions can be chained to create an XML string e.g.
You finally build the XML by running the `build()` method 

```php
$voiceActions->getDigits($options)->say($text)->record()->build();
```

##### Say

```php
$voiceActions->say($text);
```
`$text`: You can send us some text (in English) and we will read it out to the user.

##### Play

```php
$voiceActions->play($url);
```
`$url`: This element lets you play back an audio file that is located anywhere on the web.

##### Get Digits

```php
$voiceActions->getDigits($options);
```
The options array `$options` has the following keys:
- `numDigits`: 	This number of digits you would like to get from the user
- `timeout`: Timeout (in seconds) for getting the digits, after which the system moves on to the next element. If there is no other element to execute, the system will hang up
- `finishOnKey`: The key which will terminate the action of getting digits. If it is not specified, the API will send back the first digit that the user presses.
- `callbackUrl`: The parameter instructs us to forward the results of the GetDigits action to the URL value passed in. If absent, our API will forward the request to the default URL for this phone number (or to the redirected URL if a redirect has been issues).
This redirection is permanent.


##### Dial

You can use this element to connect the user who called your phone number to an external phone number.

```php
$voiceActions->dial($options);
```
The options array `$options` has the following keys:
- `phoneNumbers`: An array of phone numbers (in international format) to call. `REQUIRED`
- `record`: Boolean value of whether to record the conversation.
- `sequenntial`: Boolean If many numbers provided for `phoneNumbers`, this determines whether the phone numbers will be dialed one after the other, or whether they will all ring at the same time.
- `callerId`: This contains your AfricasTalking number you want to dial out with. It is mainly important when the caller used a sip number. If not specified in this case, the default number will be used.
- `ringBackTone`: This contains a URL location of a media playback you would want the user to listen to when the call has been placed before its picked up.
- `maxDuration`: This contains the maximum amount of time in seconds a call should take.

##### Conference

This element lets you add all the users that dial into the phone number to a conference call.
This is a terminal action. ie. No action will be executed after this.

```php
$voiceActions->conference();
```

##### Record

This action lets you record a call session into an mp3 file that you can then retrieve and play later.

###### Terminal recording

For final recording, the recording starts when the record action is called until the mobile user hangs up.

```php
$voiceActions->record();
```

###### Partial recording

Partial recording may be useful where a particular user response is required like while prompting for a name.

```php
$voiceActions->record($options);
```
The options array `$options` has the following keys:
- `finishOnKey`: The key which will terminate the action of getting digits. If it is not specified, the API will send back the first digit that the user presses.
- `maxLength`: 	Specifies the maximum amount of time in seconds a recording should take.
- `timeout`: Timeout (in seconds) for getting the digits, after which the system moves on to the next element. If there is no other element to execute, the system will hang up
- `trimSilence`: Boolean - Specifies whether you want to remove the initial and final parts of a recording where the user was silent.
- `playBeep`: Boolean - Specifies whether the API should play a beep when recording starts.
- `callbackUrl`: The parameter instructs us to forward the results of the GetDigits action to the URL value passed in. If absent, our API will forward the request to the default URL for this phone number (or to the redirected URL if a redirect has been issues).
This redirection is permanent.


##### Enqueue

Our gateway allows you to add incoming calls to a queue (with music being played in the background). You can then call a separate number to dequeue the calls one at a time.

```php
$voiceActions->enqueue($options);
```
The options array `$options` has the following keys:
- `holdMusic`: A valid URL that contains a link to the file to be played while the user is on hold. 
- `name`: You can put a call in a particular queue. eg. support, general, technical. This would help you dequeue it or know to the correct person. 


##### Dequeue

After enqueueing you can then call a separate number to dequeue the calls one at a time.
```php
$voiceActions->dequeue($options);
```
The options array `$options` has the following keys:
- `phoneNumber`: This the phone number which a user called to join the queue. `REQUIRED`
- `name`: This is the name of the queue you want to dequeue from.

##### Reject
This action lets you reject an incoming call without incurring any usage charges from our APIs. Note that this should be the only element in your response. This is a terminal action. ie. No action will be executed after this.

```php
$voiceActions->reject();
```

##### Redirect

This action will transfer control of the call to the script whose URL is passsed in. This can help you better organize your call handling logic by spreading the logic across multiple scripts

```php
$voiceActions->redirect($url);
```

##### Build

This finally builds the XML after chaining some of the actions above actions

```php
$voiceActions->build();
```

### Token

```php
$token = $AT->token();
```

#### Checkout Token

Create a checkout token.

```php
$token->createCheckoutToken($phoneNumber);
```
- `$phoneNumber`: Phone number to create an account for

### Generate Auth Token

Generate an auth token to us for authentication instead of the API key.

```php
$token->generateAuthToken();
```

### Account

```php
$account = $AT->account();
```

#### Fetch account info

Use this method to fetch account information such as the account balance

```php
$account->fetchAccount();
```

## Testing the SDK

The SDK uses [PHPUnit](https://phpunit.de/manual/current/en/index.html) as the test runner.

To run available tests, from the root of the project run:

```bash
$ phpunit
``` 


## Issues

If you find a bug, please file an issue on [our issue tracker on GitHub](https://github.com/AfricasTalkingLtd/africastalking-php/issues).
