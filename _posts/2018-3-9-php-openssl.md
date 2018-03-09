---
layout: post
title: 【PHP】使用证书对数据进行签名、验签、加密、解密以及openssl的常用方法
categories: php
description: 【PHP】使用证书对数据进行签名、验签、加密、解密以及openssl的常用方法
keywords: PHP, openssl
---

首先要使用openssl提供的函数，PHP需要此扩展：
![screenshot php_openssl](https://hufangfang1.github.io/assets/images/screenshots/php_openssl.png)

编译时加上此配置即可：`–with-openssl=/path/to/ssl`

## 首先看看如何对数据进行签名：

```
// 测试数据
$data = 'If you are still new to things, we’ve provided a few walkthroughs to get you started.';
 
// 私钥及密码
$privatekeyFile = '/path/to/private.key';
$passphrase = '';
 
// 摘要及签名的算法
$digestAlgo = 'sha512';
$algo = OPENSSL_ALGO_SHA1;
 
// 加载私钥
$privatekey = openssl_pkey_get_private(file_get_contents($privatekeyFile), $passphrase);
 
// 生成摘要
$digest = openssl_digest($data, $digestAlgo);
 
// 签名
$signature = '';
openssl_sign($digest, $signature, $privatekey, $algo);
$signature = base64_encode($signature);
 
var_dump($signature);
```
这里我们学习到了三个openssl的方法：

`openssl_pkey_get_private ( mixed $key [, string $passphrase = “” ] )` 此方法用于加载私钥。
`$key`接受的参数可以是私钥文件（协议+文件路径）或者私钥的内容。如上面代码里的方式是后者，如果换成第一种方式则是：
`$privatekey = openssl_pkey_get_private(“file://$privatekeyFile”, $passphrase);`
`$passphrase`参数为私钥的密码，如果私钥没有密码的话，可以不传或者传空。 此外，这个方法还有个别名：openssl_get_privatekey。

`openssl_digest ( string $data , string $method [, bool $raw_output = false ] )` 此方法用于对数据进行摘要计算。
`$data`是需要生成摘要的数据。
`$method`是生成摘要的算法。摘要支持哪些算法，请参照附录1.
`$raw_output`表示是否返回原始数据，如果为`false`（默认）的话则返回binhex编码后的数据。

事实上摘要的生成可以不使用openssl的这个方法，因为其实生成摘要就是对数据进行hash，我们可以用以下代码取代摘要生成部分：
``` 
<pre class="brush:php;gutter:true;first-line:1;"> if (function_exists(‘hash’)) { $digest = hash($digestAlgo, $data, TRUE); } elseif (function_exists(‘mhash’)) { $digest =mhash(constant(“MHASH_” . strtoupper($digestAlgo)), $data); } $digest = bin2hex($digest); </pre> 
```
使用hash方法有个好处就是支持的算法要比openssl_digest多很多。具体支持的算法可以调用hash_algos()方法查看。

`openssl_sign ( string $data , string &$signature , mixed $priv_key_id [, int $signature_alg = OPENSSL_ALGO_SHA1 ] ) `此方法用于对数据进行签名。 $data为需要进行签名的数据，一般为摘要。 $signature为调用成功后生成的签名。 $priv_key_id是openssl_pkey_get_private方法返回的资源标识符。 $signature_alg是签名使用的算法，默认为OPENSSL_ALGO_SHA1。支持的算法请参照附录1。

为什么要先生成摘要再签名？因为用私钥签名是比较耗时耗性能的，特别是在数据比较大的情况下。而摘要算法可以很快的生成一串很短的字符串。用这串东西去签名就快很多了。

## 下面我们看看如何验签：

```
// 测试数据，同上面一致
$data = 'If you are still new to things, we’ve provided a few walkthroughs to get you started.';
 
// 公钥
$publickeyFile = '/path/to/public.key';
 
// 摘要及签名的算法，同上面一致
$digestAlgo = 'sha512';
$algo = OPENSSL_ALGO_SHA1;
 
// 加载公钥
$publickey = openssl_pkey_get_public(file_get_contents($publickeyFile));
 
// 生成摘要
$digest = openssl_digest($data, $digestAlgo);
 
// 验签
$verify = openssl_verify($digest, base64_decode($signature), $publickey, $algo);
var_dump($verify); // int(1)表示验签成功
```
生成摘要的算法以及验签的算法必须跟签名时保持一致。

这里，我们又学习到两个新的方法：

`openssl_pkey_get_public ( mixed $certificate )` 这个方法用于加载公钥。
`$certificate`为公钥的内容。 同样，这个方法也有个别名：此外，这个方法还有个别名：openssl_get_publickey。

`openssl_verify ( string $data , string $signature , mixed $pub_key_id [, int $signature_alg = OPENSSL_ALGO_SHA1 ] ) `此方法用于验签。 $data为用于生成签名的数据。 $signature为上一步生成的签名。 $pub_key_id为加载的公钥。 $signature_alg为验签的算法。对应签名的算法。

## 简单说完签名与验签，下面我们说说加密及解密。

先看看加密：
```
// 测试数据
$data = 'If you are still new to things, we’ve provided a few walkthroughs to get you started.';
 
// 公钥
$publickeyFile = '/path/to/public.key';
 
// 加载公钥
$publickey = openssl_pkey_get_public(file_get_contents($publickeyFile));
 
// 使用公钥进行加密
$encryptedData = '';
openssl_public_encrypt($data, $encryptedData, $publickey);
 
var_dump(base64_encode($encryptedData));
```
再看看解密：
```
// 测试数据
$data = 'If you are still new to things, we’ve provided a few walkthroughs to get you started.';
 
// base64_encode过的加密数据
$encryptedData = 'xxxxxxxxxx';
 
// 私钥及密码
$privatekeyFile = '/path/to/private.key';
$passphrase = '';
 
// 加载私钥
$privatekey = openssl_pkey_get_private(file_get_contents($privatekeyFile), $passphrase);
 
// 使用公钥进行加密
$sensitiveData = '';
openssl_private_decrypt(base64_decode($encryptedData), $sensitiveData, $privatekey);
 
var_dump($sensitiveData); // 应该跟$data一致
```
很简单。这里我们又学到两个新的方法：

`openssl_public_encrypt ( string $data , string &$crypted , mixed $key [, int $padding = OPENSSL_PKCS1_PADDING ] ) `此方法用于使用公钥进行加密。
`$data`为需要加密的数据。
$crypted为加密后的数据。
$key为公钥。
$padding为填充方式，默认为OPENSSL_PKCS1_PADDING，还可以是如下几个值：OPENSSL_SSLV23_PADDING, OPENSSL_PKCS1_OAEP_PADDING, OPENSSL_NO_PADDING。

openssl_private_decrypt ( string $data , string &$decrypted , mixed $key [, int $padding = OPENSSL_PKCS1_PADDING ] ) 此方法用于使用私钥进行解密。
$data为需要解密的数据。
$decrypted为解密后的数据。
$key为私钥。
$padding为填充方式。

目前还没弄清楚各种填充方式的使用场景。

公钥加密的数据只能用私钥进行解密，而用私钥加密的数据只能用公钥进行解密。上面的代码描述的是前者。而后者对应的方法是：openssl_private_encrypt和openssl_public_decrypt。这两个方法跟上面类似，这里不作解释。

事实上，在HTTPS里，数据的加密和解密并不是直接使用公钥和私钥进行的，而是使用另外一个密钥进行对称加密跟解密。公钥跟私钥是用来加密跟解密这个密钥的。为什么要这样做了，理由跟上面讲到的签名一样，用公钥加密大数据是比较耗时耗性能的。

## 附录1 签名算法：

* 算法	                   备注
* OPENSSL_ALGO_DSS1	此常量在PHP 5.0.0中添加
* OPENSSL_ALGO_SHA1	此常量在PHP 5.0.0中添加。为openssl_sign和openssl_verify方法的默认值。
* OPENSSL_ALGO_SHA224	此常量在PHP 5.4.8中添加
* OPENSSL_ALGO_SHA256	此常量在PHP 5.4.8中添加
* OPENSSL_ALGO_SHA384	此常量在PHP 5.4.8中添加
* OPENSSL_ALGO_SHA512	此常量在PHP 5.4.8中添加
* OPENSSL_ALGO_RMD160	此常量在PHP 5.4.8中添加
* OPENSSL_ALGO_MD5	此常量在PHP 5.0.0中添加
* OPENSSL_ALGO_MD4	此常量在PHP 5.0.0中添加
* OPENSSL_ALGO_MD2	此常量在PHP 5.0.0中添加。从PHP 5.2.13和PHP 5.3.2开始，这个常量只有在PHP编译时加入MD2的支持才有效。



