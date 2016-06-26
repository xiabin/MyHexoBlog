title: SinaWeibo Provider for OAuth 2.0 Client
date: 2016-06-26 10:48:41
categories: [PHP]
tags: [PHP,Oauth2]
---


 这个组件基于 PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).
<!--more-->

## 描述
主要用于新浪微博应用的oauth2授权。

## 安装

用composer安装:

```
composer require xiabin/oauth2-sinaweibo
```

## 用例


### 获取token

```php
$provider = new Xiabin\OAuth2\Client\Provider\SinaWeibo([
    'clientId'          => '{github-client-id}',
    'clientSecret'      => '{github-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url',
]);

if (!isset($_GET['code'])) {

    //如果没有code就去获取
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

  //判断state与之前使用的是否一致
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state');

} else {

    //获取accesstoken
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    ...
}
```

## 测试

``` bash
$ ./vendor/bin/phpunit
```


## github地址

[oauth2-sinaweibo](https://github.com/xiabin/oauth2-sinaweibo)


## License

The MIT License (MIT). Please see [License File](https://github.com/xiabin/oauth2-sinaweibo/blob/master/LICENSE.md  ) for more information.

