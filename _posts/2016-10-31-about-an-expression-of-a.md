---
layout: post
title: 关于 href="javascript:;" 到底做了什么
categories: [blog ]
tags: [JavaScript, html,]
description: 对a标签的新的认识和理解
---

这几天正在仿写一个网站demo，有些细节地方想不明白就查看了网站码源学习学习，愕然发现自己的`<a>`标签与源码诸多不同，虽然之前就看过很多的网站的代码都有这个`<a href="javascript:;"></a>`或这个`<a href="javascript:void(0);></a>"`，但还是产生了疑问：

1.这种写法的作用是什么？<br>
2.为什么这样写？

于是随手Google之，排名第一的是[张鑫旭](http://www.zhangxinxu.com/wordpress/2013/01/why-use-href-javascript-void0/)大神的一篇文章，第二个就是在[stackoverflow](http://stackoverflow.com/questions/7755088/what-does-href-expression-a-href-javascript-a-do)上的问答。

由于张大神的[这篇文章](http://www.zhangxinxu.com/wordpress/2013/01/why-use-href-javascript-void0/)也是提出了自己的一些疑问而解释的较少，故在这里就不细说了。

接下来我就来看看stackoverflow上关于这个问题的高票回答。

>An `<a> `element is invalid HTML unless it has either an href or name attribute.

>If you want it to render correctly as a link (ie underlined, hand pointer, etc), then it will only do so if it has a href attribute.

>Code like this is therefore sometimes used as a way of making a link, but without having to provide an actual URL in the href attribute. The developer obviously wanted the link itself not to do anything, and this was the easiest way he knew.

>He probably has some javascript event code elsewhere which is triggered when the link is clicked, and that will be what he wants to actually happen, but he wants it to look like a normal `<a> `tag link.

>Some developers use` href='#' `for the same purpose, but this causes the browser to jump to the top of the page, which may not be wanted. And he couldn't simply leave the href blank, because `href='' `is a link back to the current page (ie it causes a page refresh).

>There are ways around these things. Using an empty bit of Javascript code in the href is one of them, and although it isn't the best solution, it does work.

看完之后真的是**豁然开朗**，故写此文以敬之。

现在我就来总结一下这位名叫[Spudley](http://stackoverflow.com/users/352765/spudley)的大神(是真的大神，被他的主页震惊了)的回答要点：

1.首先`<a>`标签要想起作用就必须有个`name`属性或是`href`属性，这是前提；
2.其次`<a>`标签若想有类似链接的效果，比如下划线或者是手状的鼠标等，就需要有`href`属性；
3.有的人会使用`href="#"`来实现相同的目的，但是`#`会使浏览器跳转到页面顶部；若使用`href=''`的话会重新刷新页面，显然也是不必要的。
4.在`href`中使用一个空的`Javascript`代码是其中的一个解决方法，虽然它不是最好的解决方案，但它可以工作。



> Written with [StackEdit](https://stackedit.io/).