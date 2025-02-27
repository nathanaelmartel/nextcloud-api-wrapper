# Nextcloud API wrapper for PHP

This is a simple php wrapper around
- [user provisioning api](https://docs.nextcloud.com/server/stable/admin_manual/configuration_user/user_provisioning_api.html?highlight=provisionning)
- [shares api](https://docs.nextcloud.com/server/latest/developer_manual/client_apis/OCS/ocs-share-api.html)

which allows you to manage your nextcloud instance dynamically : 
- create users
- change users params
- send welcome mail

##### Warning
> Nextcloud API uses basic http auth, which means username and password
are not encoded and travel the internet as clearly as possible. Make sure
to enforce it using SSL.

## Installation
Install it with composer
```
composer require nathanaelmartel/nextcloud-api-wrapper
```

## Basic usage
The library depends on Guzzle to make requests and Symfony's options resolver.
```php

use NextcloudApiWrapper\Wrapper;

//The base path to Nextcloud api entry point, dont forget the last '/'
$basePath   = 'http://my.domain.com/nextcloud/ocs/';
$username   = 'admin';
$password   = 'potatoes';

$wrapper    = Wrapper::build($basePath, $username, $password);

// https://docs.nextcloud.com/server/12/admin_manual/configuration_user/user_provisioning_api.html
$userClient                 = $wrapper->getUsersClient();
$groupsClient               = $wrapper->getGroupsClient();
$appsClient                 = $wrapper->getAppsClient();

// https://docs.nextcloud.com/server/12/developer_manual/core/ocs-share-api.html
$sharesClient               = $wrapper->getSharesClient();
$federatedCloudSharesClient = $wrapper->getFederatedCloudSharesClient();

//Instance of \NextcloudApiWrapper\NextcloudResponse
$response   = $userClient->getUsers();
$code       = $response->getStatusCode();   //status code
$users      = $response->getData();         //data as array
$message    = $response->getStatus();       //status message
$guzzle     = $response->getRawResponse();  //Guzzle response

// create user 
$userClient->addUser('nathanaelmartel', 'password', 'Nathanaël Martel', 'nathanael@simplement-web.com', 'fr');
```

### Making your own requests
If you'd like to perform your own requests, you can use the underlying
nextcloud connection class to perform them.
```php
$connection = new \NextcloudApiWrapper\Connection($basePath, $username, $password);

//To perform simple requests
$response   = $connection->request('GET', 'cloud/users');

//To perform requests which needs the 'application/x-www-form-urlencoded' header
//and are not performed in POST
$response   = $connection->pushDataRequest('PUT', 'cloud/' . $username . '/disable');

//To perform requests which holds some values to submit
$response   = $connection->submitRequest('POST', 'cloud/users', [
    'userid'    => 'potatoes',
    'password'  => 'tortilla'
]);
```

### Licence
**MIT**
