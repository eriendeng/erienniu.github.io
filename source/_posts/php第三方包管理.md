---
title: php第三方包管理
date: 2019-05-12 00:45:26
tags:
---

## 前言
在编写程序的时候，我们会用到别人的第三方包，俗称轮子。如何正确引入一个第三方的包，且正确合理的放置，是一个值得规范的事情。

## 传统的包引入
```php
include "the/path/of/file.php";
require "the/path/of/file.php";
include_once "the/path/of/file.php";
require_once "the/path/of/file.php";

function_name();
?>
```
优点：我们比较熟悉包内结构，文件位置<br/>
缺点：我们必须清楚文件结构；引入大量的`inlude`,`require`语句；不能实现懒加载

## 新的包引入方式
`__autoload()`魔术方法：当运行时调用了当前没有声明过的类时，自动运行该函数。</br>
```php
<?php

function __autoload($class_name){
    require "./new_package/class2.php";
}

(new hello())->sayHello();
```
我们可以在我们文件中重新定义这个函数，并添加我们函数中的逻辑去实现文件的加载，这种方式属于懒加载，一定程度上能节省资源。<br/><br/>
`__autoload()`函数必须传入参数（尽管我们也许不会使用）<br/>
在原本autoload中，同一个文件中只能支持同时存在一个autoload函数，重复定义会引起panic。

## autoload的改良
在php5.5后，引入了spl改良版的autoload系列函数，使用`spl_autoload_register`或者`set_include_path`和`spl_autoload`的组合可以很快引入某路径下的类。
```php
<?php
//
//spl_autoload_register(function ($class_name){
//   if ($class_name === 'hello'){
//       include "./new_package/class2.php";
//   }
//});

set_include_path("./new_package/"); //这里需要将路径放入include
spl_autoload("class2");

(new hello())->sayHello();
```

优点：懒加载，重新定义/抽象了require和include<br/>
缺点：仍无法解决大量的引入代码问题；可能产生循环引用


## 包管理工具Composer
[composer](https://getcomposer.org/)是php项目中的一个开源第三方包管理工具，是一个让人眼前一亮的工具，它将autoload的思想发挥到了新的层次。

一个使用composer来管理第三方包的项目通常会在项目根目录下包含vender文件夹，里面放有我们的第三方包。

composer使用方法：
```
composer init//初始化一个目录成为composer管理项目
composer require "xxxxx@1.0.*"

//如果已经存在别人的composer.json文件
composer install
//升级版本
composer update
//删除
composer remove "xxxx"
```

composer通过生成一个总的引入类来引入所有的第三方依赖，我们只需要在整个项目的一个地方引入`require "./vender/autoload.php";`这样一个文件，就会去自动执行整个需要文件的执行。<br/>

composer.json中的`autoload`和`autoload_dev`说明了整个项目中的依赖关系的命名关系
```
"autoload": {
        "psr-4": {
            "App\\": "app/"
        },
        "classmap": [
            "database/seeds",
            "database/factories"
        ]
    },
```
我们可以直接使用`\App\Hello`来代表在app目录下的Hello类，composer会自动实现命名空间到文件目录的转换。

[这篇博客](https://blog.csdn.net/weixin_44039145/article/details/86028492)中有更详细的命名映射方法。

* [命名空间](https://www.php.net/manual/zh/language.namespaces.rationale.php) 

#### composer引入的版本号

表达式 | 意义 |  实例 | 含义  
-|-|-|-
数字 | 指定版本 | 1.2.3 | 下载1.2.3版本
~符号 | 维持小版本| ~1.2.3 | 下载1.3.0前的版本
^符号 | 维持大版本 | ^1.2.3 | 下载2.0.0前的版本
*符号 | 任意版本 | 1.2.* | 下载1.2中任意一个版本
运算符 | 运算符含义 | >=1.2.3 | 下载大于1.2.3的版本
@符号 | 选择分支 | 1.2.3@dev | 下载dev分支中的1.2.3版本

多个条件支持,和|分割，代表and or逻辑运算，如`composer require xxx ^1.2.0@dev,!=1.2.3`

#### 注意
不要随意改动composer.lock文件中的内容<br/>
注意将vender文件夹从版本控制中去除


