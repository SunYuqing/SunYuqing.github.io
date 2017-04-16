---
layout: post
title: 关于异步请求的一些事
categories: [blog ]
tags: [asyns, ]
description: 关于前端的异步请求的一些注意问题
---

先看这样一个例子：
```
function foo() {
    var result;

    $.ajax({
        url: '...',
        success: function(response) {
            result = response;
        }
    });

    return result;
}

var result = foo(); // It always ends up being `undefined`.

```

在这个例子中，使用ajax进行异步请求，但是响应的返回值始终是undefined。那这是什么原因呢？这个问题最初也让我很困惑。下面我们来分析一下原因。

##找出问题的原因

我们知道ajax中的a代表异步，那就意味着发送请求（亦或是接受响应）需要花费一点时间。而在上述例子中，`$.ajax`是被立即返回的，`return result;` 这句语句在`success` 函数返回响应前就已经被执行了。主要原因还是没有理解异步和同步之间的区别。

下面这个类比希望可以使同步和异步的区别更清晰。

###Synchronous（同步流）

想象你给你的一个朋友打电话，你希望他可以帮你一个忙，你会进行一段时间的等待，直到他的回答。

那么在正常同步流的代码中也会发生一样的事情。

```
function findItem() {
    var item;
    while(item_not_found) {
        // search
    }
    return item;
}

var item = findItem();

// Do something with item
doSomethingElse();
```
执行 `findItem` 需要花费一段时间，所有在 `var item = findItem();` 这条语句之后的代码都需要等待函数返回结果。

###Asynchronous(异步流)

同样你又给你的朋友打了一个电话，但是现在你很忙，你给你的朋友留言让他给你回一个电话，然后你挂掉电话就做其他的事情了，直到你的朋友回你电话，你才会停下手中的事情处理这个电话。

**这也正是ajax请求的过程。**

```
findItem(function(item) {
    // Do something with item
});
doSomethingElse();
```

不去等待响应而是立即继续执行ajax之后的语句。而为了得到响应我们会定义一个回调函数，一旦收到响应就会执行这个回调函数。但是注意在ajax请求之后的语句会在回调函数调用前被执行。

##解决问题的方法

### 1. 使用回调函数

上面的例子中  

```
var result = foo();
// Code that depends on 'result'

``` 

改为
```
foo(function(result) {
    // Code that depends on 'result'
});
```
然后我们给 `foo` 传递函数参数

```
function myCallback(result) {
    // Code that depends on 'result'
}

foo(myCallback);
```
函数 `foo` 定义如下

```
function foo(callback) {
    $.ajax({
        // ...
        success: callback
    });
}
```
一旦`ajax` 请求成功，`$.ajax` 就会调用`callback` 函数并且将响应传递给`callback` 函数。

###2.使用promise

Promise API是ES6新特性之一，但是各大浏览器已经有了很好的支持(IE11及以上支持)。MDN这么定义它：

>Promise 对象用于异步计算。一个 Promise 表示一个现在、将来或永不可能可用的值

promise的语法：

```javascript
new Promise(
    /* executor */
    function(resolve, reject) {...}
);
```

这里是一段使用`promise` 的例子：

```javascript
function delay() {
		// `delay` returns a promise
  return new Promise(function(resolve, reject) {
	    // Only `delay` is able to resolve or reject the promise
    setTimeout(function() {
      resolve(42); 
	    // After 3 seconds, resolve the promise with value 42
    }, 3000);
  });
}

delay().then(function(v) { 
		// `delay` returns a promise
	  console.log(v); 
	    // Log the value once it is resolved
}).catch(function(v) {
	    // Or do something else if it is rejected 
	    // (it would not happen in this example, since `reject` is not called).
});
```
结合`ajax` 使用：

```
function ajax(url) {
  return new Promise(function(resolve, reject) {
    var xhr = new XMLHttpRequest();
    xhr.onload = function() {
      resolve(this.responseText);
    };
    xhr.onerror = reject;
    xhr.open('GET', url);
    xhr.send();
  });
}

ajax("/echo/json").then(function(result) {
  // Code depending on result
}).catch(function() {
  // An error occurred
});
```
[这里](http://www.html5rocks.com/en/tutorials/es6/promises/)有关于`promise` 的更多信息。

### 3.jQuey: 使用延迟函数

jQery的延迟函数和`promise` 很像，但是API略有不同。每一个jQery的ajax方法都会返回一个延迟函数。

```javascript
function ajax() {
    return $.ajax(...);
}

ajax().done(function(result) {
    // Code depending on result
}).fail(function() {
    // An error occurred
});
```


始终要牢记`promise` 和延迟函数都只有一个存有未来值 的容器而不是值本身。看下面的例子：

```javascript
function checkPassword() {
    return $.ajax({
        url: '/password',
        data: {
            username: $('#username').val(),
            password: $('#password').val()
        },
        type: 'POST',
        dataType: 'json'
    });
}

if (checkPassword()) {
    // Tell the user they're logged in
}
```

这段代码就误解了异步和延迟过程，`$.ajax()` 会向服务器发送对`/password` 的请求，jQery内部机制会立即会返回一个ajax延迟函数，而这个并不是服务器发回来的响应。这也就意味着下面的判断语句（if）始终都是true,程序会认为读者已经登录，而这是不正确的。

我们可以进行一个小的修改：

```javascript
checkPassword()
.done(function(r) {
    if (r) {
        // Tell the user they're logged in
    } else {
        // Tell the user their password was bad
    }
})
.fail(function(x) {
    // Tell the user something bad happened
});
```

现在我们仍然会向服务器发起请求，`$.ajax()` 也会立即一个延迟对象，但是我们通过监听`.done()` 和`.fail()` 事件能正确处理服务器返回的响应。`.done()` 事件被调用时，服务器返回的是一个正常的响应（http 200），我们通过检查他的返回对象是否为true判断用户是否登陆。
`.fail()` 处理函数是处理一些错误的事件。例如用户网络断开或服务器错误等。

>本文代码均来自StackOverflow上[这个话题](http://stackoverflow.com/questions/14220321/how-do-i-return-the-response-from-an-asynchronous-call)。
>
> Written with [StackEdit](https://stackedit.io/).