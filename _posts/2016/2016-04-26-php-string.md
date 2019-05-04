---
layout: post
title: "[PHP] 基础语法-字符串操作"
date: 2016-04-26
author: mdgsf
comments: true
categories: PHP
tags: [Php]
description:
published: true #default true
---

```php
<html>
<head>
</head>

<body>

<h2>Hello World</h2>
<p>Today is <?php echo date('M j, Y') ?></p>

<?php

//变量名区分大小写
$capital = 67;
print("Variable capital is $capital<br>");
print("Variable capital is $CaPiTal<br>");


$myName = 'Andy';
echo "strlen('$myName') = " . strlen($myName) . "<br>";  //输出 4


//去掉字符串前后的空格
$myVar = '   the book of days    ';
echo trim($myVar);


//在界面上显示的都是 Andy & Amos， 但是如果用浏览器查看html源代码，
//第一个是Andy & Amos， 第二个是Andy &amp; Amos
$myName = "Andy & Amos";
echo $myName . "<br>";
echo htmlspecialchars($myName) . "<br>";


//Upper Case First changes the first character to uppercase,
//output: "The book of days"
$myVar = 'the book of days';
echo ucfirst($myVar) . "<br>";


//Upper Case Words changes the first character of each word to uppercase,
//output: "The Book Of Days"
$myVar = 'the book of days';
echo ucwords($myVar) . "<br>";


//String to Lower converts any uppercase letters to lowercase.
//output: "the book of days"
$myVar = 'THE BOOK OF DAYS';
echo strtolower($myVar) . "<br>";


//String to Upper converts any lowercase to uppercase.
//output: "THE BOOK OF DAYS"
$myVar = 'the book of days';
echo strtoupper($myVar) . "<br>";


//output: "The Book Of Days"
$myVar = 'THE BOOK OF daYs';
echo ucwords( strtolower($myVar) );



phpinfo();

?>

</body>
</html>
```
