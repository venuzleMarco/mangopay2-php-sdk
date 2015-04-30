Mangopay SDK
=================================================
MangopaySDK is a PHP client library to work with
[Mangopay REST API](http://docs.mangopay.com/api-references/).


Installation
-------------------------------------------------
SDK has been written in PHP 5.5 and has no dependencies on external packages.
You only have to ensure that curl and openssl extensions (that are part of
standard PHP distribution) are enabled in your PHP installation.

The project attempts to comply with PSR-4 specification for autoloading classes from file paths. 
As a namespace prefix is 'MangoPay\' with base directory '{your-installation-dir}/'.

But if not using PSR-4 the installation is as easy as downloading the package and storing it
under any location that will be available for including by

    require_once '{your-installation-dir}/MangoPay/Autoloader.php';

in your project (see examples below).

Installation with Composer
-------------------------------------------------
You can use Mangopay SDK library as a dependency in your project with Composer. A composer.json file is available in the repository and it has been referenced on packagist. 

The installation with Composer is easy, reliable : 
Step 1 - Add the Mangopay SDK as a dependency in your composer.json file as follow :

    "require": {
        ...
        "mangopay/php-sdk-v2": "1.4.*"
    },

Step 2 - Update your dependencies with Composer

    you@yourhost:/path/to/project$ php composer.phar update mangopay/php-sdk-v2

The Library has been added into your dependencies and ready to be used.

License
-------------------------------------------------
MangopaySDK is distributed under MIT license, see LICENSE file.


Unit Tests
-------------------------------------------------
Tests are placed under {your-installation-dir}/tests.
The /tests/suites/all.php suite runs ALL tests.
You can also use any of /tests/cases/*.php to run a single test case.


Contacts
-------------------------------------------------
Report bugs or suggest features using
[issue tracker at GitHub](https://github.com/Mangopay/mangopay2-php-sdk).


Client creation example (you need to call it only once)
-------------------------------------------------

    require_once '{your-installation-dir}/MangoPay/Autoloader.php';
    $api = new MangoPay\MangoPayApi();

    $client = $api->Clients->Create(
        'your-client-id', 
        'your-client-name', 
        'your-client-email@sample.org'
    );

    // you receive your password here, note it down and keep in secret
    print($client->Passphrase);


Configuration
-------------------------------------------------
See the example above and call `$api->Clients->Create` once to get your passphrase.
Then set `$api->Config->ClientId` to your Mangopay Client ID and 
`$api->Config->ClientPassword` to your passphrase.

You also need to set a folder path in `$api->Config->TemporaryFolder` that SDK needs 
to store temporary files. This path should be outside your www folder.
It could be `/tmp/` or `/var/tmp/` or any other location that PHP can write to. 
**You must use different folders for your sandbox and production environments.**

`$api->Config->BaseUrl` is set to sandbox environment by default. To enable production
environment, set it to `https://api.mangopay.com`.

    require_once '{your-installation-dir}/MangoPay/Autoloader.php';
    $api = new MangoPay\MangoPayApi();

    // configuration
    $api->Config->ClientId = 'your-client-id';
    $api->Config->ClientPassword = 'your-client-password';
    $api->Config->TemporaryFolder = '/some/path/';
    //$api->Config->BaseUrl = 'https://api.sandbox.mangopay.com';

    // call some API methods...
    $users = $api->Users->GetAll();


Sample usage
-------------------------------------------------

    require_once '{your-installation-dir}/MangoPay/Autoloader.php';
    $api = new MangoPay\MangoPayApi();

    // configuration
    $api->Config->ClientId = 'your-client-id';
    $api->Config->ClientPassword = 'your-client-password';
    $api->Config->TemporaryFolder = '/some/path/';

    // get some user by id
    $john = $api->Users->Get($someId);

    // change and update some of his data
    $john->LastName .= " - CHANGED";
    $api->Users->Update($john);

    // get all users (with pagination)
    $pagination = new MangoPay\Pagination(1, 8); // get 1st page, 8 items per page
    $users = $api->Users->GetAll($pagination);

    // get his bank accounts
    $pagination = new MangoPay\Pagination(2, 10); // get 2nd page, 10 items per page
    $accounts = $api->Users->GetBankAccounts($john->Id, $pagination);


Sample usage of Mangopay SDK installed with Composer in a Symfony project
-------------------------------------------------
You can integrate Mangopay features in a Service in your Symfony project. 

MangoPayService.php : 

    <?php

    namespace Path\To\Service;
    
    use MangoPay;
    
    
    class MangoPayService
    {
    
        private $mangoPayApi;
    
        public function __construct()
        {
            $this->mangoPayApi = new MangoPay\MangoPayApi();
            $this->mangoPayApi->Config->ClientId = 'your-client-id';
            $this->mangoPayApi->Config->ClientPassword = 'your-client-password';
            $this->mangoPayApi->Config->TemporaryFolder = '/some/path/';    
            //$this->mangoPayApi->Config->BaseUrl = 'https://api.sandbox.mangopay.com';
        }
        
        /**
         * Create Mangopay User
         * @return MangopPayUser $mangoUser
         */
        public function getMangoUser()
        {
            
            $mangoUser = new \MangoPay\UserNatural();
            $mangoUser->PersonType = "NATURAL";
            $mangoUser->FirstName = 'John';
            $mangoUser->LastName = 'Doe';
            $mangoUser->Birthday = 1409735187;
            $mangoUser->Nationality = "FR";
            $mangoUser->CountryOfResidence = "FR";
            $mangoUser->Email = 'john.doe@mail.com';
    
            //Send the request
            $mangoUser = $this->mangoPayApi->Users->Create($mangoUser);
    
            return $mangoUser;
        }
    }
