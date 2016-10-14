---
layout: post
title: 关于javascript中闭包的一些认识
categories: [blog ]
tags: [JavaScript, closure,]
description: 对JavaScript闭包的认识和理解
---
##1.闭包是什么?
 [MDN](https://developer.mozilla.org/zh-CN/) 上对闭包(closure)的定义为
>Closures are functions that refer to independent (free) variables (variables that are used locally, but defined in an enclosing scope). In other words, these functions 'remember' the environment in which they were created.

意思就是：
>闭包是指那些能够访问独立(自由)变量的函数 (变量在本地使用，但定义在一个封闭的作用域中)。换句话说，这些函数可以“记忆”它被创建时候的环境。

##2.闭包的优点有哪些？
闭包的特点是函数嵌套函数，内部函数可以引用外部函数的参数和变量。参数和变量不会被[垃圾回收](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management)机制所收回。
见下面的例1:
```
function foo(a){
	var a = 123;
	return function fo(){
		alert(a);	
	}
}
var b = foo();
b();
```
例1中外部函数声明的**变量a**不会被回收（因为内部函数在引用a），b( )的结果可用console查看。这种闭包的好处之一就是**可以使一个变量长期驻扎的内存中**。  
闭包还有一个优点就是**避免全局变量的污染**。见下面的例子  
例2:
```
var a = 5;
function foo(){
	a++;
	alert(a);
}
foo();//6
foo();//7
console.log(a)//7 全局变量a被污染
```
例3:
```
function foo(){
	var a = 5;
	a++;
	alert(a);
}
foo();//6
foo();//6 说明a调用完后被垃圾回收了，故没有进行累加
console.log(a);// a在函数内属于局部变量，全局没有定义的情况下，控制台提示错误：a is not defined
```
例4:
```
function foo(){
	var a = 5;
	return function fo(){
		a++;
		alert(a);
	}
}
var b = foo();
b();//6
b();//7
console.log(a);//控制台提示错误：a is not defined
```
例4可以改写成一个匿名函数,执行时立即调用。这种写法一般称为**模块化代码**，既不会污染全局变量也方便调用:
```
var b = (function(){
	var a = 5;
	return function(){
		a++;
		alert(a);
	}
})();
b();//6
b();//7
```

例5:
```
var aaa=(function(){
	var a = 5;
	function bbb(){
		a++;
		alert(a);
	}
	function ccc(){
		a++;
		alert(a);
	}
	return{
		b:bbb,
		c:ccc
	}
})();
aaa.b();//6
aaa.c();//7
alert(a);//a is not defined
alert(bbb)//bbb is not defined  
```
例4和例5中都说明闭包中存在**私有成员**。
##3.闭包的用法
闭包的用法除了上面说的**模块化代码**外还可以用来**在循环中直接找到对应元素的索引**。
例6:
html部分：
```
<ul>
	<li>小明</li>
	<li>小红</li>
	<li>小庆</li>
</ul>
```
js部分：
```
window.onload = function(){
	var aLi = document.getElementsByTagName("li");//获取所有元素
	for(var i=0;i<aLi.length;i++){
		aLi[i].onclick = function(){
			alert(i);
		}
	}
}
```
上面的代码经常出现在各种面试题中，那么代码结果是什么呢？结果就是无论你点哪一个名字，浏览器都会弹出 **3**！这是因为当你在点击之前，for循环已经执行完毕，此时的i就是3。这种情况显然不是我们所期望的。

那么我们又如何利用闭包避免上述情况呢，其实很简单。我们利用上述所说的**闭包可以使一个变量永远驻扎在内存中**的这个优点，具体方法见下面的例子。
例7:
```
我们将例6中js改写为
window.onload = function(){
	var aLi = document.getElementsByTagName("li");
	for(var i=0;i<aLi.length;i++){
		(function(){
			aLi[i].onclick=function(){
				alert(i);
			}
		})(i)
	}
}
```
这样，内部函数就可以调用外部函数传进来的参数且外部函数执行完毕也不会影响内部函数。还可以通过return返回值来改写，见下面的例子。
```
for(var i=0;i<aLi.length;i++){
	aLi[i].onclick=(function(){
		return function(){
			alert(i);
		}
	})(i)
}
```
通过以上两种改写，我们就达到了我们想要的效果了。
##4.闭包需要注意的地方
闭包在老的IE（主要IE6）下由于垃圾回收机制有问题会导致[内存泄漏](https://zh.wikipedia.org/wiki/%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)。那么什么是**内存泄漏**呢？维基百科是这么描述的:
>内存泄漏指由于疏忽或错误造成程序未能释放已经不再使用的内存。内存泄漏并非指内存在物理上的消失，而是应用程序分配某段内存后，由于设计错误，导致在释放该段内存之前就失去了对该段内存的控制，从而造成了内存的浪费。

解决方法可以在window的onunload里将事件设为null，具体推荐这篇博文：[浅析闭包和内存泄漏问题](http://blog.leanote.com/post/rongdee/%E6%B5%85%E6%9E%90%E9%97%AD%E5%8C%85%E5%92%8C%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E7%9A%84%E9%97%AE%E9%A2%98)。