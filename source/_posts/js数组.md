---
title: JavaScript数组常用方法全面指南
date: 2023-10-18 19:28:00
categories:
  - 教程指南
tags:
  - JavaScript
  - 数组
description: 本文涵盖了JavaScript中常用的数组方法,包括增删改查、搜索和位置、排序、操作和迭代等方面。通过掌握这些方法,你可以更好地处理数组数据,提高前端学习和工作效率。
cover: https://pic3.zhimg.com/v2-f4e9a15d2e61567ac00cdfdf836628cc_720w.jpg?source=172ae18b
---

## 引言

数组是JavaScript中最常用也最重要的数据结构之一。合理地利用数组的各种操作方法,可以大大简化代码,提高开发效率。

本文将全面介绍JavaScript中数组的各种常用方法,包括数组元素的增、删、改、查,以及数组的排序、搜索、迭代等操作。通过学习本文,你将系统地掌握数组的用法,在处理数组数据时能够灵活应用,在前端开发中更得心应手。

下面让我们正式开始JavaScript数组方法的学习之旅吧!

## 一、增删改方法

数组操作是我们经常需要用到的功能,常见的增删改查方法可以帮助我们对数组进行操作。下面将介绍五种常见的增删方法。

### push()

push()方法用于向数组末尾添加一个或多个元素,并返回数组的最新长度。

```js
let colors = ["red"];
let count = colors.push("green","blue");
console.log(count); // 3 
console.log(colors); // ["red","green","blue"]
```

### unshift()

unshift()方法用于向数组开头添加一个或多个元素,并返回新的数组长度。

```js
let colors = ["red"];
let count = colors.unshift("green","blue");
console.log(count); // 3
console.log(colors); // ["green","blue","red"]
```

### pop()

pop()方法用于删除数组的最后一项,并返回被删除的项。

```js
let colors = ["red","green","blue"];
let item = colors.pop();
console.log(item); // blue
console.log(colors); // ["red","green"]
```

### shift()

shift()方法用于删除数组的第一项,并返回被删除的项。

```js
let colors = ["red","green","blue"]; 
let item = colors.shift();
console.log(item); // red
console.log(colors); // ["green","blue"]
```

### splice()

splice()方法可以在任意位置对数组进行增删改操作。它接受三个参数:开始位置、要删除元素的数量、要插入的任意多个元素,并返回删除元素的数组。这个方法会对原数组产生影响。

```js
let colors = ["red", "green", "blue"];
let removed = colors.splice(1, 1, "red", "purple"); 
console.log(colors); // ["red","red","purple","blue"]
console.log(removed); // ["green"]
```

## 二、搜索和位置方法

在处理数组时,我们经常需要查找特定的元素,JavaScript提供了一些方法来帮助我们实现这个目标。 

### indexOf()

indexOf()方法返回数组中第一次出现指定元素的索引,如果没有找到该元素则返回-1。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
console.log(numbers.indexOf(9)); // -1
console.log(numbers.indexOf(4)); // 3
```

### lastIndexOf()

lastIndexOf()方法返回数组中最后一次出现指定元素的索引,如果没有找到该元素则返回-1。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];  
console.log(numbers.lastIndexOf(9)); // -1
console.log(numbers.lastIndexOf(4)); // 5
```

### find()

find()方法返回数组中满足条件的第一个元素。

```js
const people = [
  { name: "张三", age: 27 },
  { name: "李四", age: 29 }  
];
let result = people.find((item, index, array) => item.age < 28);
console.log(result); // { name: "张三", age: 27 }
```

### findIndex() 

findIndex()方法返回数组中满足条件的第一个元素的索引,如果没有找到则返回-1。

```js
const people = [
  { name: "张三", age: 27 },
  { name: "李四", age: 29 }
];
let index = people.findIndex((item, index, array) => item.age === 28); 
console.log(index); // -1
```

### includes()

includes()方法返回一个布尔值,表示数组是否包含指定的元素。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
console.log(numbers.includes(4)); // true
console.log(numbers.includes(9)); // false
```

## 三、排序方法 

JavaScript提供了两个方法用于对数组进行排序。

### reverse()

reverse()方法用于反转数组中的元素顺序,改变原数组。

```js
let values = [1, 2, 3, 4, 5];
values.reverse();  
console.log(values); // [5,4,3,2,1]
```

### sort()

sort()方法用于对数组进行排序,可以接受一个回调函数来指定排序规则。

```js
let values = [4, 2, 3, 1, 5];
values.sort((a,b) => a - b); // 升序
console.log(values); //[1, 2, 3, 4, 5]

values.sort((a,b) => b - a); // 降序
console.log(values); //[5, 4, 3, 2, 1]  
```

## 四、操作方法

JavaScript提供了一些常用的操作方法,用于对数组进行操作。

### join() 

join()方法将数组转换为字符串,并使用指定的分隔符连接数组的每一项,但不会改变原数组。

```js
let colors = ["red", "green", "blue"];
console.log(colors.join(",")); // "red,green,blue" 
console.log(colors.join("||")); // "red||green||blue"
```

### slice()

slice()方法用于截取数组的一部分,返回一个新的数组,不会影响原数组。它接受两个参数,分别是开始截取的下标和结束的下标(不包含结束下标的元素)。如果只有一个参数,则从开始下标截取到末尾。

```js
let colors = ["red", "green", "blue", "yellow", "purple"]; 
let colors2 = colors.slice(1); // 从下标1开始截取到末尾
let colors3 = colors.slice(1, 4); // 从下标1开始截取到下标4(不包含)
console.log(colors); // ["red", "green", "blue", "yellow", "purple"]
console.log(colors2); // ["green", "blue", "yellow", "purple"]  
console.log(colors3); // ["green", "blue", "yellow"]
```

### concat()

concat()方法用于连接两个或多个数组,返回一个新的数组,不会改变原数组。

```js
let colors = ["red", "green", "blue"];
let colors2 = colors.concat("yellow", ["black", "brown"]);
console.log(colors); // ["red", "green", "blue"]  
console.log(colors2); // ["red", "green", "blue", "yellow", "black", "brown"]
```

## 五、迭代方法

JavaScript提供了一些用于迭代数组的方法,它们可以帮助我们对数组进行遍历和处理。

### some()

some()方法对数组中的每一项执行回调函数,如果有一项满足函数的条件,则返回true。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let someResult = numbers.some((item, index, array) => item > 2);
console.log(someResult); // true
```

### every()

every()方法对数组中的每一项执行回调函数,如果每一项都满足函数的条件,则返回true。

```js 
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let everyResult = numbers.every((item, index, array) => item > 2);
console.log(everyResult); // false
```

### forEach()

forEach()方法对数组中的每一项执行回调函数,没有返回值,类似于for循环。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
numbers.forEach((item, index, array) => {
  // 执行某些操作
});
```

### filter()

filter()方法对数组中的每一项执行回调函数,返回满足条件的项组成的新数组。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];  
let newNumbers = numbers.filter((item, index, array) => item > 2);
console.log(newNumbers); // [3, 4, 5, 4, 3]
```

### map()

map()方法对数组中的每一项执行回调函数,返回由每次函数调用的结果构成的新数组。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let mapResult = numbers.map((item, index, array) => item * 2);
console.log(mapResult); // [2, 4, 6, 8, 10, 8, 6, 4, 2]
```

### reduce() 

reduce()方法用于对数组进行累加操作,接受两个参数,第一个参数为回调函数,第二个参数为初始值。如果没有初始值,则默认为数组的第一个元素。

```js
const arr = [1, 2, 3, 4, 5];  
arr.reduce((pre, item, index, arr) => {
  pre += item;
  return pre;
}, 0);
```
## 结语

以上为js数组中常用的方法,掌握了这些方法,你可以更好地处理数组数据,在前端的学习和工作中发挥更大的作用。希望本文对你有所帮助!

请注意:本文为原创内容,转载请注明出处。

