---
layout: post
title: php闭包使用例子
categories: php
description: php 闭包
keywords: php
---

## 一.依据闭包实现一个容器

```php

class Di
{
    private $factory;
 
    public function set($id, $value)
    {
        $this->factory[$id] = $value;
    }
 
    public function get($id)
    {
        $val = $this->factory[$id];
        return $val();//如果不加括号,仅仅返回的是闭包类,并不是User实例
    }
}
 
class User
{
    private $username;
 
    public function __construct($username = '')
    {
        $this->username = $username;
    }
 
    public function getUserName()
    {
        return $this->username;
    }
}
 
$di = new Di();
 
// 在此使用了闭包,所以实际上并不会实例化User类,只有在后面get的时候才会实例化
$di->set('a', function(){
    return new User('张三');
});
 
var_dump($di->get('a')->getUserName());

```

## 二.使用闭包作为回调

```php

class Cart
{
    CONST PRICE_BUTTER = 1.0;
    CONST PRICE_MILK = 5.05;
 
    protected $products = [];
 
    public function add($product, $quantity)
    {
        $this->products[$product] = $quantity;
    }
 
    public function getQuantity($product)
    {
        return isset($this->products[$product]) ? $this->products[$product]: false;
    }
 
    public function getTotal($tax)
    {
        $total = 0.00;
        $callback = function($quantity, $product) use ($tax, &$total) {
            $priceItem = constant(__CLASS__ . '::PRICE_' . strtoupper($product));
            $total += ($priceItem * $quantity) * ($tax + 1.0);
        };
 
        array_walk($this->products, $callback);
        return round($total, 2);
    }
}
 
$cart = new Cart();
$cart->add('butter', 1);
$cart->add('milk', 5);
 
echo $cart->getTotal(0.05);

```


##  三.使用闭包函数调用类中方法

```php
class Grid
{
    protected $builder;
    protected $attribute;
 
    public function __construct(Closure $builler)
    {
        $this->builder = $builler;
    }
 
    public function addColumn($name, $value)
    {
        $this->attribute[$name] = $value;
        return $this;
    }
 
    public function build()
    {
        // 这儿回调闭包函数,参数为this
        call_user_func($this->builder, $this);
    }
 
    public function __toString()
    {
        $this->build();
 
        $str = '';
        $call = function($val, $key) use(&$str) {
             $str .= "$key=>$val;";
        };
        array_walk($this->attribute, $call);
 
        return $str;
    }
}
 
$grid = new Grid(
    // 传入闭包函数,带参数
    function($grid) {
        $grid->addColumn('key1', 'val1');
        $grid->addColumn('key2', 'val2');
    }
);
 
echo $grid;

```

