---
title: js异步调用和阻止
date: 2018-07-26 16:14:04
tags:
---

# js的异步调用精髓和阻止方法

## 异步调用

> JavaScript引擎是单线程运行的,浏览器无论在什么时候都只且只有一个线程在运行JavaScript程序。
> JavaScript引擎用单线程运行也是有意义的,单线程不必理会线程同步这些复杂的问题,问题得到简化。

为了弥补js单线程机制，引入异步调用机制
![js流程](http://images2015.cnblogs.com/blog/638135/201607/638135-20160721111145247-341211472.png)

在event loop中存在一个类似带有出口的死循环机制，在完成function主体部分后开始被调用。

```JavaScript

XXX: function(){
  //do sth...
  Success: function() {
    /*
    callback body
      do sth before
      Add sth to TODO
    */
  }
}

```

异步操作会检测回调任务队列总中的任务，完成后将之后新的检测添加到个循环中</br>
由于callback结束时间无法准确给出，所以js中程序调用的先后级只规定在function主体中。

> 优点：将剩下待处理的交给callback，延后function结束时间，类似创建监视器。
> 缺点：下一个function运行所需的数据仍在回调加载中，会给出错误。

[js异步机制](https://blog.csdn.net/qdq2014/article/details/72383725/)

## 回调阻止

对于上面的缺点，一般有两种方法规避。

> 将下一个进行的function写入上一个function的callback中
> 引入Promise对象

主要讲Promise对象 `Promise.all`</br>

Promise规范如下：
> 一个promise可能有三种状态：等待（pending）、已完成（fulfilled）、已拒绝（rejected）
> 一个promise的状态只可能从“等待”转到“完成”态或者“拒绝”态，不能逆向转换，同时“完成”态和“拒绝”态不能相互转换
> promise必须实现then方法（可以说，then就是promise的核心），而且then必须返回一个promise，同一个promise的then可以调用多次，并且回调的执行顺序跟它们被定义时的顺序一致。
> then方法接受两个参数，第一个参数是成功时的回调，在promise由“等待”态转换到“完成”态时调用，另一个是失败时的回调，在promise由“等待”态转换到“拒绝”态时调用。同时，then可以接受另一个promise传入，也接受一个“类then”的对象或方法，即thenable对象。

```JavaScript

function getImg(url) {  
  var p = Promise();  
  var img = new Image();  
  
  //当img生成时会触发onload函数，在onload函数中将Promise对象的状态设为完成
  img.onload = function() {  
    p.resolve(this);  
  };

  //出错时设为拒绝调用下一步
  img.onerror = function(err) {  
    p.reject(err);  
  };  
  img.url = url;
  
  //返回整体程序的完成程度  
  return p;  
};  

```

以上代码可以写为

```
function getImg(url) {  
  return Promise(function(resolve, reject) {  
    var img = new Image();  
    img.onload = function() {  
      resolve(this);  
    };  
  
    img.onerror = function(err) {  
      reject(err);  
    };  
  
    img.url = url;  
  });  
};

```

总体：通过Promise状态决定下一步调用
