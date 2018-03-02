---
layout: post
title: Linux 安装composer
categories: Linux
description: Composer 是 PHP 的一个依赖管理工具。它允许你申明项目所依赖的代码库，它会在你的项目中为你安装他们。。
keywords: Linux, PHP, Composer
---

## 1、下载COMPOSER.PHAR

`curl -sS https://getcomposer.org/installer | php`

## 2、全局调用
```
 1 mv composer.phar /usr/local/bin/composer
 2 chmod +x composer
 ```

## 3、测试安装结果

```
composer about
```