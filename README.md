# JSON Web Token Class

A simple and lightweight class to create, sign, and verify JSON Web Tokens (JWT).  The class also has a method that creates secrets with over double the length needed to ensure the creation of secure signatures.  The [JWT standard](https://tools.ietf.org/html/rfc7519 "RFC 7519") requires that implementations must support HS256 and "none" as valid algorithms (*all others are optional*).  This implementation currently supports HS256, HS384, HS512, and "none".  Support for both RS and ES equivalent algorithms will be added in future versions.

## Getting Started

### Prerequisites

Before using this class, you may want to [brush up on JWT](https://jwt.io/ "jwt.io") if you are not already familiar with it.  In short, JSON Web Tokens are used as a self-contained method of providing stateless authentication and/or to exchange verifiable information with a trusted party.

### Installing

##### Via Composer

Run the following command in the same directory as your composer.json file:

`php composer.phar require jabarihunt/json-web-token`

##### Via Github

1. Clone this repository into a working directory: `git clone git@github.com:jabarihunt/JSON-Web-Token.git .`

2. Include the JWT class in your project...

```php
require('/path/to/JWT.php')
```
...or if using an auto-loader...
```php
 use jabarihunt/JWT;
```

## Usage

##### Choosing An Algorithm

This class will default to the HS256 algorithm.  If you want to use one of the other algorithms, pass it optionally in the `JWT::sign()` and `JWT::generateSecret()` methods.  Supported algorithms are class constants.  For example sake, we'll set an algorithm variable to use HS384...

```php
$algorithm = JWT::ALGORITHM_HS384;
```

##### Creating A Secret

You are free to pass any secret you like when using the `JWT::sign()` and `JWT::verify()` methods.  However, to create a very secure signature you ***MUST*** use a secret that is at least as long as the number of bits of encryption.  For example, if using HS256, you should use a secret that is at least 256 bits (32 bytes) long.

> ***NOTE:*** Secrets should be stored in a secure location (*secure configuration include, .env, etc.*) and NOT within your script.  Additionally, the generated secret is not URL safe, though, it should ***NEVER*** be passed around via HTTP requests anyway!

In our example, we will generate a secret and pass along the optional `$algorithm` parameter using the variable of the same name we created above...

```php
$secret = JWT::generateSecret($algorithm);
var_dump($secret);
```

```php
/* OUTPUT */
/var/www/html/controllers/HomeController.php:5:string 'e69KMtdewPimnO8zMwgwuVJUSgdbtMMFdib+Eo8TL/Jk2+NkONvZ9QYUm0U2sH93/qliaqMOGZz8vv0Y8Dh/SYWoTNRwniYg4M289GigKIQbDyBk3uNWGIGRtO7H1QkZ' (length=128)
```

##### Creating A JSON Web Token

JSON Web Tokens are created with the `JWT::sign()` method which takes *two* required parameters and *one* optional parameter:

`(array) $payload` - An array containing the data to be transmitted.

`(string) $secretOrPrivateKey` - For the HS256, HS384, and HS512 algorithms, it expects this to be a secret (as generated above). For the "none" algorithm, simply pass `NULL` (it will ignore any value passed since no signature will be appended). For all other algorithms, it expects a string path to the ***private*** key file used to encrypt the signature.

`(string) $algorithm` (*optional*) - The name of the algorithm to be used for signing (*it defaults to "HS256"*).  All supported  algorithms may be accessed as class constants.

Putting it together with the secret (*which is being pulled from `$_ENV`*) and algorithm examples from above...

```php
<?php

    $payload = ['iat' => time(), 'uid' => 357];
    $secret = $_ENV['JWT_SECRET'];
    $algorithm = JWT::ALGORITHM_HS384;

    $token = JWT::sign($payload, $secret, $algorithm);
    var_dump($token);
?>
```

```php
/* OUTPUT: */
/var/www/html/controllers/HomeController.php:8:string 'eyJhbGciOiJIUzM4NCIsInR5cGUiOiJKV1QifQ.eyJpYXQiOjE1NDMwNDE0MjcsInVpZCI6MzU3fQ.ZjUzYzA5N2FhZGRlOGQ0Yzg2OWY0OWJiMmNmZGI3NjY3MTc4YWNhMTcyNzc3Y2ZlOGJkNzdlOWZhMTQxM2Y4NTE1ZjM4ZTBjY2RmOWY3MmQ2M2JhYjgxM2U3NmExOTM0' (length=206)
```

##### Verifying A JSON Web Token

Tokens are verified using the `JWT::verify()` method which expects *two* parameters:

`(string) $token` - A standard JSON Web Token.

`(string) $secretOrPrivateKey` - For the HS256, HS384, and HS512 algorithms, it expects this to be a secret (*as generated above*). For the "none" algorithm, simply pass `NULL` (*it will ignore any value passed since no signature will be appended*). For all other algorithms, it expects a string path to the ***public*** key file that matches the private key used to encrypt the signature.

Using the token generated above:

```php
<?php

    $token = 'eyJhbGciOiJIUzM4NCIsInR5cGUiOiJKV1QifQ.eyJpYXQiOjE1NDMwNDE0MjcsInVpZCI6MzU3fQ.ZjUzYzA5N2FhZGRlOGQ0Yzg2OWY0OWJiMmNmZGI3NjY3MTc4YWNhMTcyNzc3Y2ZlOGJkNzdlOWZhMTQxM2Y4NTE1ZjM4ZTBjY2RmOWY3MmQ2M2JhYjgxM2U3NmExOTM0';
    $secret = $_ENV['JWT_SECRET'];
    
    var_dump(JWT::verify($token, $secret));
    
?>
```

The `JWT::verify()` method will return an array containing the following keys:

`(boolean) isVerified` - **TRUE** if the token's signature was valid, **FALSE** otherwise.  ***ALWAYS CHECK THIS!!!***

`(array) header` - The decoded header of the token.

`(array) payload` - The decoded payload of the token.

```php
/* OUTPUT: */
/var/www/html/controllers/HomeController.php:6:
array (size=3)
  'isVerified' => boolean true
  'header' => 
    array (size=2)
      'alg' => string 'HS384' (length=5)
      'type' => string 'JWT' (length=3)
  'payload' => 
    array (size=2)
      'iat' => int 1543041427
      'uid' => int 357
```

## Contributing

1. Fork Repository
2. Create a descriptive branch name
3. Make edits to your branch
4. Squash (rebase) your commits
5. Create a pull request

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details