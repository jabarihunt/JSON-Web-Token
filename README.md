# JSON Web Token Class

A simple and lightweight class to create, sign, and verify JSON Web Tokens (JWT).  The class also has a method that creates secrets with double the minimun length needed to ensure the creation of secure signatures.  The [JWT standard](https://tools.ietf.org/html/rfc7519 "RFC 7519") requires that implementations must support HS256 and "none" as valid algorithms (all others are optional).  This implementation currently supports HS256, HS384, HS512, and "none".  Support for both RS and ES eqivalent algorithms will be added in future versions.

## Getting Started

### Prerequisites

Before using this class, you may want to [brush up on JWT](https://jwt.io/ "jwt.io") if you are not already familiar with it.  In short, JSON Web Tokens are used as a self-contained method of providing stateless authentication and/or to exchange verifiable information with a trusted party.

### Installing

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

##### Via Composer

Instructions will be updated as soon as it is active in composer.

## Usage

##### Creating A Secret

You are free to pass any secret you like when using the `sign()` and `verify()` methods.  However, to create a very secure signature you ***MUST*** use a secret that is at least as long as the number of bits of encryption.  For example, if using HS256, you should use a secret that is at least 256 bits (32 bytes) long.

> ***NOTE:*** Secrets should be stored in a secure location (configuration include, .env, etc.) and NOT within your script.  Additionally, the generated secrets are not URL safe, though, it should NEVER be passed around via server requests aanyway!

In our example, we will generate a secret and pass along the optional `$algorithm` paramater using the variable of the same name we created above...

```php
$secret = JWT::generateSecret($algorithm);
```

##### Choosing An Algorithm

This class will default to the HS256 algorithm.  If you want to use one of the other algorithms, pass it optionally in the `sign()` and `generateSecret()` methods.  Supported algorithms are class constants.  For example sake, we'll set an algorithm variable to use HS384...

```php
$algorithm = JWT::ALGORITHM_HS384;
```

##### Creating A JSON Web Token

JSON Web Tokens are created with the `sign()` method which takes three different parameters:

`(Array) $payload` - An array containing the data to be transmitted.

`(String) $secretOrPrivateKey` - For the HS256, HS384, and HS512 algorithms, it expects this to be a secret (as generated above). For the "none" algorithm, simply pass `NULL` (it will ignore any value passed since no signature will be appended). For all other algorithms, it expects a string path to the private key file used to encrypt the signature.

`(String) $algorithm` (optional) - The name of the algarithm to be used for signing (it defaults to HS256).  All supported  algorithms may be accessed as class constants.

Putting it together with the secret (pulling from `$_ENV`) and algarithm examples from above...

```php
<?php

    $payload   = ['iat' => time(), 'uid' => 357];
    $secret      = $_ENV['JWT_SECRET'];
    $algorithm = JWT::ALGORITHM_HS384;
    $token       = JWT::sign($payload, $secret, $algorithm);

    var_dump($token);

?>
```


## Contributing

1. Fork Repository
2. Create a descriptive branch name
3. Make edits to your branch
4. Squash (rebase) your commits
5. Create a pull request

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details