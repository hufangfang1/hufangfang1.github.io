---
layout: post
title: TP5 where数组查询(模糊查询)(有多个查询条件)
categories: thinkphp5
description: thinkphp5 where
keywords: thinkphp5, PHP, where
---
有查询条件就查询，

多个查询条件，只要有查询，就增加一个查询条件
## 1、TP5.1版本：
模糊查询
```php
    $where[] = ['title','like',"%".$sotitle."%"];
  
     $map[] = ['name','like','think'];
     $map[] = ['status','=',1];
      
     //注意：
     $where[] = ['exp',Db::raw("FIND_IN_SET($id,category)")];//category值为数字，
      
     //一但用到$where[]方式和where('type',1)不能同时存在一个语句中
     //下面这语句的其它条件全部失效
      
      
     //正确写:
     $where = "FIND_IN_SET($category_id,category)";
      
     $crs=Db::name('product')->field('id,title')->where('type',1)->where('type_mold',1)->where('deleted',0)->whereLike('title',"%".$sotitle."%")->where($where)->select()
```
in查询 
```php
    $where1 = [  
                                ['role_id', 'in', '1,2,3'],  
                            ];
```


TP5.1.21   版本之后数组查询支持：


要达到这样子查询：

    1、首先引用： use think\db\Where;
    
    2、定义数组：$where = new Where;
    
    3、就可以用了：$where['title'] = ['like', "%".$sotitle."%"];





