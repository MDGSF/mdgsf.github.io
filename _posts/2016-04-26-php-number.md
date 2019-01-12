---
layout: post
title: "[PHP] 基础语法-number"
date: 2016-04-26
author: mdgsf
comments: true
categories: php
tags: php
description:
published: true #default true
---

```php
<?php
/**
 * Created by PhpStorm.
 * User: JIAN.HUANG
 * Date: 2016/4/26
 * Time: 8:52
 */

echo "number demo<br>";


$stringNumber = '3';
$number = 5;
echo $stringNumber * $number; //output: 15
echo "<br>";


//the $stringNumber is converted to 15
$stringNumber = '15a4';
$number = 5;
echo $stringNumber * $number; //output: 75
echo "<br>";


//the $stringNumber is converted to 0
$stringNumber = 'aaa';
$number = 5;
echo $stringNumber * $number; //output: 0
echo "<br>";


//The variables are converted to strings
$stringNumber = 3;
$number = 5;
echo $stringNumber . $number; //output: 35
echo "<br>";


$number = '15';
echo number_format($number, 2); //output: 15.00
echo "<br>";


$number = 1234.56;
echo number_format($number); //output: 1235
echo "<br>";


$number = 1234.56;
echo number_format($number, 2, ',', ' '); //output: 1 234,56
echo "<br>";


$number = 1234.5678;
echo number_format($number, 2, '.', ''); //output: 1234.57
echo "<br>";


$number = 1234.5618;
echo number_format($number, 2, '.', ''); //output: 1234.56
echo "<br>";

```


### number_format

```php
string number_format ( float $number [, int $decimals = 0 ] )
```

```php
string number_format ( float $number , int $decimals = 0 , string $dec_point = "." , string $thousands_sep = "," )
```


本函数可以接受1个、2个或者4个参数（注意：不能是3个）:

如果只提供第一个参数，number的小数部分会被去掉 并且每个千位分隔符都是英文小写逗号","

如果提供两个参数，number将保留小数点后的位数到你设定的值，其余同楼上

如果提供了四个参数，number 将保留decimals个长度的小数部分, 小数点被替换为dec_point，千位分隔符替换为thousands_sep
