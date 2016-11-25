---
layout: post
title: Intermediate Algorithm Scripting
categories: [blog ]
tags: [JavaScript, Algorithm,]
description: 数组，字符串的常见方法及中级算法
---

本文是继续[上文](http://yuqingsun.com/blog/Basic-Algorithm-Scripting.html)内容进行进阶补充。

## Sum All Numbers in a Range

>传递给你一个包含两个数字的数组。返回这两个数字和它们之间所有数字的和。
>最小的数字并非总在最前面。

```
function sumAll(arr) {
  var max=Math.max.apply(null,arr);
  var min=Math.min.apply(null,arr);
  var sum=[];
  //方法一
  //for(var i=min;i<=max;i++){
  //  sum+=i;
  //}
  //return sum;
  //方法二
  for(var i=min;i<=max;i++){
    sum.push(i);
  }
  return sum.reduce(function(x,y){
    return x+y;
  });
}

sumAll([1, 4]);
```
## Diff Two Arrays
>比较两个数组，然后返回一个新数组，该数组的元素为两个给定数组中所有独有的数组元素。换言之，返回两个数组的差异。

```
function diff(arr1, arr2) {
  var newArr = [];
  var newArr1=[];
  newArr=arr1.concat(arr2);
  newArr1=newArr.filter(function(ele){
    return arr1.indexOf(ele)===-1 || arr2.indexOf(ele)===-1;
  });
  return newArr1;
}

diff([1, 2, 3, 5], [1, 2, 3, 4, 5]);
```

## Roman Numeral Converter
>将给定的数字转换成罗马数字。
>所有返回的 罗马数字 都应该是大写形式。

```
function convert(num) {
  var nums = [1000,900,500,400,100,90,50,40,10,9,5,4,1];
  var romans =["m","cm","d","cd","c","xc","l","xl","x","ix","v","iv","i"];
  var str = '';
  nums.forEach(function(item,index,array){
    while(num >= item){
      str += romans[index];
      num -= item;
    }
  });
  
 return str.toUpperCase();
}
```

## Where art thou
>写一个 function，它遍历一个对象数组（第一个参数）并返回一个包含相匹配的属性-值对（第二个参数）的所有对象的数组。如果返回的数组中包含 source 对象的属性-值对，那么此对象的每一个属性-值对都必须存在于 collection 的对象中。
>例如，如果第一个参数是 [{ first: "Romeo", last: "Montague" }, { first: "Mercutio", last: null }, { first: "Tybalt", last: "Capulet" }]，第二个参数是 { last: "Capulet" }，那么你必须从数组（第一个参数）返回其中的第三个对象，因为它包含了作为第二个参数传递的属性-值对。

```
function where(collection, source) {
  var arr = [];
  var keys = Object.keys(source);
  arr = collection.filter(function(obj) {
    return keys.every(function(key) {
      return obj.hasOwnProperty(key) && obj[key] === source[key];
    });
  });

  return arr;
}

where([{ first: "Romeo", last: "Montague" }, { first: "Mercutio", last: null }, { first: "Tybalt", last: "Capulet" }], { last: "Capulet" });

```

## Search and Replace
>使用给定的参数对句子执行一次查找和替换，然后返回新句子。
>第一个参数是将要对其执行查找和替换的句子。
>第二个参数是将被替换掉的单词（替换前的单词）。
>第三个参数用于替换第二个参数（替换后的单词）。
>注意：替换时保持原单词的大小写。例如，如果你想用单词 "dog" 替换单词 "Book" ，你应该替换成 "Dog"。

```javascript
function myReplace(str, before, after) {
  var index = str.indexOf(before);
  if (str[index] === str[index].toUpperCase()) {
    after = after.charAt(0).toUpperCase() + after.slice(1);
  }
  str = str.replace(before, after);
  console.log(str);

  return str;
}
myReplace("A quick brown fox jumped over the lazy dog", "jumped", "leaped");
```

##Pig Latin
>把指定的字符串翻译成 pig latin。
>Pig Latin 把一个英文单词的第一个辅音或辅音丛（consonant cluster）移到词尾，然后加上后缀 "ay"。
>如果单词以元音开始，你只需要在词尾添加 "way" 就可以了。

```javascript
function translate(str) {
  var str1='';
  var reg=/[aeiou]/gi;
  if(str[0].match(reg)){
    str1=str+"way";
  }else{
    var index=str.indexOf(str.match(reg)[0]);
    str1=str.substr(index)+str.substr(0,index)+"ay";
  }
  
  return str1;
}

translate("consonant");

```

## Missing letters
>从传递进来的字母序列中找到缺失的字母并返回它。如"abce"返回"d"。
>如果所有字母都在序列中，返回 undefined。

```javascript
function fearNotLetter(str) {
  
  var firstCharacter = str.charCodeAt(0);
  var valueToReturn = '';
  var secondCharacter = '';

  var addCharacters = function(a, b) {
    while (a - 1 > b) {
      b++;
      valueToReturn += String.fromCharCode(b);
    }

    return valueToReturn;
  };

  for (var index = 1; index < str.length; index++) {
    secondCharacter = str.charCodeAt(index);
    if (secondCharacter - firstCharacter > 1) {
      
      addCharacters(secondCharacter, firstCharacter);
    }
    firstCharacter = str.charCodeAt(index);
  }
  
  if (valueToReturn === '')
    return undefined;
  else
    return valueToReturn;
}

fearNotLetter("abcg");
```
## Sorted Union
>写一个 function，传入两个或两个以上的数组，返回一个以给定的原始数组排序的不包含重复值的新数组。
>换句话说，所有数组中的所有值都应该以原始顺序被包含在内，但是在最终的数组中不包含重复值。
>非重复的数字应该以它们原始的顺序排序，但最终的数组不应该以数字顺序排序。

```
function unite(arr1, arr2, arr3) {
  var args = Array.prototype.slice.call(arguments);
  var arr = args.reduce(function(prev,cur,index,array){
    return prev.concat(cur);
  });
  return arr.filter(function(item,index,array){
    return array.indexOf(item) === index;
  });
}
unite([1, 3, 2], [5, 2, 1, 4], [2, 1]);

```
## Convert HTML Entities
>将字符串中的字符 &、<、>、" （双引号）, 以及 ' （单引号）转换为它们对应的 HTML 实体。

```
function convert(str) {
  var temp = str.split('');
  for (var i = 0; i < temp.length; i++) {
    switch (temp[i]) {
      case '<':
        temp[i] = '&lt;';
        break;
      case '&':
        temp[i] = '&amp;';
        break;
      case '>':
        temp[i] = '&gt;';
        break;
      case '"':
        temp[i] = '&quot;';
        break;
      case "'":
        temp[i] = '&apos;';
        break;
    }
  }

  temp = temp.join('');
  return temp;
}
convert("Dolce & Gabbana");
```

## Spinal Tap Case
>将字符串转换为 spinal case。Spinal case 是 all-lowercase-words-joined-by-dashes 这种形式的，也就是以连字符连接所有小写单词。

```
function spinalCase(str) {
  str = str.replace(/_/g," ")
        .replace(/([A-Z])/g," $1")//$1表示匹配第一个小括号
        .replace(/^\s/,"")
        .replace(/\s+/g,"-")
        .toLowerCase();
  return str;
}

spinalCase('This Is Spinal Tap');
```
## Sum All Odd Fibonacci Numbers
>给一个正整数num，返回小于或等于num的斐波纳契奇数之和。
>斐波纳契数列中的前几个数字是 1、1、2、3、5 和 8，随后的每一个数字都是前两个数字之和。
>例如，sumFibs(4)应该返回 5，因为斐波纳契数列中所有小于4的奇数是 1、1、3。

提示：此题不能用递归来实现斐波纳契数列。因为当num较大时，内存会溢出，推荐用数组来实现。

```javascript
var sumFibs = function() {
    var cache = [1, 1];
    return function (n) {
        if (n >= cache.length) {
            for (var i = cache.length; i < n ; i++ ) {
                cache[i] = cache[i - 2] + cache[i - 1];
            }
        }
        var arr=cache.filter(function(val){
           return val%2!==0&&val<=n;
         });
        return arr.reduce(function(pre,next){
          return pre+next;
        });
    };

}();

sumFibs(4);
```

## Sum All Primes
>求小于等于给定数值的质数之和。
>只有 1 和它本身两个约数的数叫质数。例如，2 是质数，因为它只能被 1 和 2 整除。1 不是质数，因为它只能被自身整除。
>给定的数不一定是质数。

```
function sumPrimes(num) {
  var arr = [];
  var ifPrime = function(num){
    if(num < 2){
      return false;
    }
    if(num === 2){
      return true;
    }
    if(num % 2 === 0){
      return false;
    }
    for(var i = 3; i <= Math.sqrt(num); i+=2){
      if(num % i === 0){
        return false;
      }
    }
    return true;
  };
  for(var i = 2; i <= num; i++){
    if(ifPrime(i)){
      arr.push(i);
    }
  }
  return arr.reduce(function(prev,cur,index,array){
     return prev + cur;
  },0);
}
sumPrimes(10);
```

## Smallest Common Multiple
>找出能被两个给定参数和它们之间的连续数字整除的最小公倍数。
>范围是两个数字构成的数组，两个数字不一定按数字顺序排序。
>例如对 1 和 3 —— 找出能被 1 和 3 和它们之间所有数字整除的最小公倍数。

```javascript
function smallestCommons(arr) {
  var min = Math.min(arr[0],arr[1]);
  var max = Math.max(arr[0],arr[1]);
  var _arr = [];
  for(var i = min; i <= max; i++){
    _arr.push(i);
  }
  var gcd = function(a,b){
    if(b){
      return gcd(b, a % b);
    }
    return a;
  };
  return _arr.reduce(function(prev,cur,index,array){
    return prev*cur/gcd(prev,cur);
  });
}
smallestCommons([1,5]);
```
## Drop it
>让我们来丢弃数组(arr)的元素，从左边开始，直到回调函数return true就停止。
>第二个参数，func，是一个函数。用来测试数组的第一个元素，如果返回fasle，就从数组中抛出该元素(注意：此时数组已被改变)，继续测试数组的第一个元素，如果返回fasle，继续抛出，直到返回true。
>最后返回数组的剩余部分，如果没有剩余，就返回一个空数组。

```
function drop(arr, func) {
  
  while(!func(arr[0])&&arr.length>0){
    arr.shift();
  }
  return arr;
}

drop([1, 2, 3], function(n) {return n < 3; });

```

## Steamroller
>对嵌套的数组进行扁平化处理。你必须考虑到不同层级的嵌套。
>如：steamroller([[["a"]], [["b"]]]) 应该返回 ["a", "b"]。
>steamroller([1, [2], [3, [[4]]]]) 应该返回 [1, 2, 3, 4]。

```
function steamroller(arr){	
  var result = [];
  for(var i = 0; i < arr.length; i++){
    if(Array.isArray(arr[i])){
      result = result.concat(steamroller(arr[i]));
    }else{
      result.push(arr[i]);
    }
  }
  return result;
}

steamroller([1, [2], [3, [[4]]]]);

```
## Binary Agents
>传入二进制字符串，翻译成英语句子并返回。
>二进制字符串是以空格分隔的。

```
function binaryAgent(str) {
  var arr = str.split(" ");
  arr = arr.map(function(item,index,array){
    return String.fromCharCode(parseInt(item,2));
  });
  
  return arr.join("");
}

binaryAgent("01000001 01110010 01100101 01101110 00100111 01110100 00100000 01100010 01101111 01101110 01100110 01101001 01110010 01100101 01110011 00100000 01100110 01110101 01101110 00100001 00111111");
```

## Everything Be True
>完善编辑器中的every函数，如果集合(collection)中的所有对象都存在对应的属性(pre)，并且属性(pre)对应的值为真。函数返回ture。反之，返回false。
>记住：你只能通过中括号来访问对象的变量属性(pre)。
>提示：你可以有多种实现方式，最简洁的方式莫过于[Array.prototype.every()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every)。

```
function every(collection, pre) {
  // Is everyone being true?
  return collection.every(function(item,index,array){
    return item[pre];
  });
}

every([{"user": "Tinky-Winky", "sex": "male"}, {"user": "Dipsy", "sex": "male"}, {"user": "Laa-Laa", "sex": "female"}, {"user": "Po", "sex": "female"}], "sex");
```

## Arguments Optional
>创建一个计算两个参数之和的 function。如果只有一个参数，则返回一个 function，该 function 请求一个参数然后返回求和的结果。
>例如，add(2, 3) 应该返回 5，而 add(2) 应该返回一个 function。
>调用这个有一个参数的返回的 function，返回求和的结果：
>var sumTwoAnd = add(2);
>sumTwoAnd(3) 返回 5。
>如果两个参数都不是有效的数字，则返回 undefined。

```
function add() {
  if(typeof arguments[0] !== "number" || (arguments.length > 1 && typeof arguments[1] !== "number")){
    return undefined;
  }
  if(arguments.length == 1){
    var arg0 = arguments[0];
    return function(num){
      if(typeof num !== "number"){
        return undefined;
      }
      return arg0 + num;
    };
  }else{
    return arguments[0] + arguments[1];
  }
}

add(2,3);

```

> Written with [StackEdit](https://stackedit.io/).