---
layout: post
title: Basic Algorithm Scripting
categories: [blog ]
tags: [JavaScript, Algorithm,]
description: 数组，字符串的常见方法及基本算法
---
最近知道了一个学习前端的好去处，[FreeFodeCamp](www.freecodecamp.com),里面资源很全，有很多实战的项目且都是开源公益性质的，前后端，数据库都有，社区也很活跃。FreeFodeCampc崇尚的是*Read-Search-Ask'*，即遇到问题时查文档，Google之，社区询问之。FreeFodeCamp也有[中文网](www.freecodecamp.cn),貌似内容没有区别，就是打开速度稍慢。

下面是我挑选的[FreeFodeCamp](www.freecodecamp.com)一个系列课程中关于数组，字符串一些算法，这些解决方案一些是自己想的，一些是Google和stackover上的。在[FreeFodeCamp](www.freecodecamp.com)里这节课叫做 Basic Algorithm Scripting，即为基本算法。

## Reverse a String

>翻转字符串
>先把字符串转化成数组，再借助数组的reverse方法翻转数组顺序，最后把数组转化成字符串。
>你的结果必须得是一个字符串

解决方法：

```JavaScript
function reverseString(str) {
  var str1=str.split("");
  return str1.reverse().join("");
 
}

reverseString("hello");
```

## Factorialize a Number 

>计算一个整数的阶乘
>如果用字母n来代表一个整数，阶乘代表着所有小于或等于n的整数的乘积。
>阶乘通常简写成 n!
>例如: 5! = 1 * 2 * 3 * 4 * 5 = 120

解决方案：

```
function factorialize(num) {
  if(num===0){
    return 1;
  }else{
    return num*factorialize(num-1);
  }
}

factorialize(5);
```

## Check for Palindromes 

>如果给定的字符串是回文，返回true，反之，返回false。
>如果一个字符串忽略标点符号、大小写和空格，正着读和反着读一模一样，那么这个字符串就是palindrome(回文)。
>注意你需要去掉字符串多余的标点符号和空格，然后把字符串转化成小写来验证此字符串是否为回文。
>函数参数的值可以为"racecar"，"RaceCar"和"race CAR"。

解决方案：

```
function palindrome(str) {
    str = str.replace(/[^a-zA-Z0-9]+/gi, '').toLowerCase();
    //这里注意正则表达式的含义：[^a-zA-Z0-9]为反向字符集，匹配任何没有包含在方括号中的字符。
    return str == str.split('').reverse().join('');
}
palindrome("eye");
```

## Find the Longest Word in a String 

>找到提供的句子中最长的单词，并计算它的长度。
>函数的返回值应该是一个数字。

解决方案：

```
function findLongestWord(str) {
 
  var words = str.split(/\s+/);
  var longest = '';

  for (var i = 0; i < words.length; i++) {
    if (words[i].length > longest.length) {
        longest = words[i];
    }
  }
  return longest.length;
}

findLongestWord("The quick brown fox jumped over the lazy dog");
```

## Title Case a Sentence 

>确保字符串的每个单词首字母都大写，其余部分小写。
>像'the'和'of'这样的连接符同理。

解决方案：

```
function titleCase(str) {
  var str1=str.toLowerCase().split(/\s+/);
  
  for(var i=0;i<str1.length;i++){
    var str2=str1[i].slice(1).toLowerCase();
    str1[i]=str1[i][0].toUpperCase()+str2;
  }
  str1=str1.join(" ");
  return str1;
}

titleCase("I'm a little tea pot");
```

## Return Largest Numbers in Arrays 

>右边大数组中包含了4个小数组，分别找到每个小数组中的最大值，然后把它们串联起来，形成一个新数组。
>提示：你可以用for循环来迭代数组，并通过arr[i]的方式来访问数组的每个元素。

解决方案：

```
function largestOfFour(arr) {
  // You can do this!
  var arr1=[];
  for(var i=0;i<arr.length;i++){
      arr1.push(Math.max.apply(Math,arr[i]));
    }
  return arr1;
  }

largestOfFour([[4, 5, 1, 3], [13, 27, 18, 26], [32, 35, 37, 39], [1000, 1001, 857, 1]]);
```

## Chunky Monkey

>把一个数组arr按照指定的数组大小size分割成若干个数组块。
>例如: chunk([1,2,3,4],2)=[[1,2],[3,4]];
> chunk([1,2,3,4,5],2)=[[1,2],[3,4],[5]];

解决方案：

```javascript
function chunk(arr, size) {
  var arr1 = [];
  for(var i = 0;i < arr.length;i += size){
    arr1.push(arr.slice(i,i + size));
  }
  return arr1;
}
chunk(["a", "b", "c", "d"], 2);

```

## Seek and Destroy

>实现一个摧毁(destroyer)函数，第一个参数是待摧毁的数组，其余的参数是待摧毁的值。
>例如：
>destroyer([1, 2, 3, 1, 2, 3], 2, 3) 应该返回 [1, 1].
>destroyer([1, 2, 3, 5, 1, 2, 3], 2, 3) 应该返回 [1, 5, 1].

```
function destroyer(arr) {
  var args = Array.prototype.slice.call(arguments);
  args.splice(0,1);
  return arr.filter(function(element) {
   //逆向思维 :filter会遍历element中的每个元素，（注释：element为args中的元素）然后经过filter函数
    //返回后为true的会加入到一个数组里返回。所有不是待摧毁的值会被返回回来。
    return args.indexOf(element) === -1;
   // alert(element);
  });
}
destroyer([1, 2, 3, 1, 2, 3], 2, 3);
```

另外一种解法：

```
unction destroyer(arr) 
  var args = [];
  for(var i = 1; i < arguments.length; i++){
    args.push(arguments[i]);
  }
  var temp = arr.filter(function(item,index,array){
    return args.indexOf(item) < 0;
  });
  return temp;
}

```

## Confirm the Ending 

>检查一个字符串(str)是否以指定的字符串(target)结尾。
>如果是，返回true;如果不是，返回false。

解决方案：

```
  var length=target.length;
  return str.substr(-length)===target?true:false;
 }

confirmEnding("Bastian", "n");
	
```

## Repeat a string repeat a string 

>重要的事情说3遍！
>重复一个指定的字符串 num次，如果num是一个负数则返回一个空字符串。

解决方案：

```

function repeat(str, num) {
  var l=[];
  while(l.length<num){
    l.push(str);
  }
  return l.join("");
}

repeat("abc", 3);

```

>
## Truncate a string 

>用瑞兹来截断对面的退路!
>截断一个字符串！
>如果字符串的长度比指定的参数num长，则把多余的部分用...来表示。
>切记，插入到字符串尾部的三个点号也会计入字符串的长度。
>但是，如果指定的参数num小于或等于3，则添加的三个点号不会计入字符串的长度。

解决方案：

```
unction truncate(str, num) {
  var str1='';
  if(str.length>num&&num>3){
   str1=str.slice(0,(num-3))+"...";
  }else if(str.length>num&&num<=3){
    str1=str.slice(0,num)+"...";
  }else{
    str1=str;
  }
  return str1;
}

truncate("A-tisket a-tasket A green and yellow basket", 11);

```

## Mutations

>蛤蟆可以吃队友，也可以吃对手。
>如果数组第一个字符串元素包含了第二个字符串元素的所有字符，函数返回true。

>举例，["hello", "Hello"]应该返回true，因为在忽略大小写的情况下，第二个字符串的所有字符都可以在第一个字符串找到。
>["hello", "hey"]应该返回false，因为字符串"hello"并不包含字符"y"。
>["Alien", "line"]应该返回true，因为"line"中所有字符都可以在"Alien"找到。

解决方案：

```
function mutation(arr) {
  var a = arr[0].toLowerCase();
  var b = arr[1].toLowerCase();
  for(var i = 0; i < b.length; i++){
    if(a.indexOf(b[i]) < 0){
      return false;
    }
  }
  return true;
}
mutation(["hello", "hey"]);
```

## Falsy Bouncer 

>真假美猴王！
>删除数组中的所有假值。
>在JavaScript中，假值有false、null、0、""、undefined 和 NaN。

解决方案：

```
function bouncer(arr) {
   return arr.filter(Boolean);
}

bouncer([7, "ate", "", false, 9]);

```

## Where do I belong 

>我身在何处？
>先给数组排序，然后找到指定的值在数组的位置，最后返回位置对应的索引。
>举例：where([1,2,3,4], 1.5) 应该返回 1。因为1.5插入到数组[1,2,3,4]后变成[1,1.5,2,3,4]，而1.5对应的索引值就是1。
>同理，where([20,3,5], 19) 应该返回 2。因为数组会先排序为 [3,5,20]，19插入到数组[3,5,20]后变成[3,5,19,20]，而19对应的索引值就是2。

解决方案：

```
function where(arr, num) {
  arr.push(num);
  arr.sort(function(a,b){
    return a>b;
  });
  
  return arr.indexOf(num);
}

where([40, 60], 50);

```

## Caesars Cipher 

>让上帝的归上帝，凯撒的归凯撒。
>下面我们来介绍风靡全球的凯撒密码Caesar cipher，又叫移位密码。
>移位密码也就是密码中的字母会按照指定的数量来做移位。
>一个常见的案例就是[ROT13](http://www.baike.com/wiki/ROT13&prd=so_1_doc)密码，字母会移位13个位置。由'A' ↔ 'N', 'B' ↔ 'O'，以此类推。
>写一个ROT13函数，实现输入加密字符串，输出解密字符串。
>所有的字母都是大写，不要转化任何非字母形式的字符(例如：空格，标点符号)，遇到这些特殊字符，跳过它们。

解决方案：

```
function rot13(str) { 
  var index = null;
  var temp = "";
  var _A = "A".charCodeAt(0);
  var _Z = "Z".charCodeAt(0);
  var mid = (_A + _Z)/2;
  for(var i = 0; i < str.length; i++){
    index = str.charCodeAt(i);
    if(index >= _A && index <= mid ){
      temp += String.fromCharCode(index + 13);
    }else if(index <= _Z && index > mid ){
      temp += String.fromCharCode(index - 13);
    }else{
      temp += String.fromCharCode(index);
    }   
  }
  return temp;
}

rot13("SERR PBQR PNZC");
```


> Written with [StackEdit](https://stackedit.io/).