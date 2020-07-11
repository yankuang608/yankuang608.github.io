---
layout: detail
title: instanceof 的模拟实现
description: 模拟实现 instanceof 关键字
tags: 原生JS
category: front_end
img_url: /JS.png
img_1: /prototype.png
---
# {{ page.title }}
## 写在前面的话
这篇文章来源于一道面试题《如何判断一个对象是否为一个构造函数的实例》想当初为了回答这道题，我扯了很久的原型链最后给出了一句 **obj.\__proto__ === constructor.prototype** 。如果在给我一次机会，我会潇洒地说出 **instanceof** 关键字，而面试官接下来问的如何实现 **instanceof** 关键字，就是这篇文章要介绍的了。
## obj.\__proto__ === constructor.prototype
如果熟悉[原型链](http://https://yankuang608.github.io/front_end/prototype/)的话就知道，其实一开始提出的这个方法是有可行之处的，不熟悉的话也可以回顾下面这张图。
![Image]({{site.imgurl}}{{ page.img_1}})
但是也会有下面几个问题
## 继承
观察下面这段代码我们发现，instanceof 是会受继承关系影响的，如果对象是由该构造函数的“子函数”实例化所得，同样会返回 true 。
```js
console.log(b instanceof Foo);  // true
console.log(b instanceof Object);   // true 
```
要想实现这点，可以运用递归的方法。先判断 obj 是否为 constructor 的实例，如果不是则令 **obj = obj.\__proto__** 继续判断，直到 obj 等于 Object.prototype 。
```js
function isInstanceof(obj, constructor){
    if (obj === Object.prototype){return false;}
    if !( obj.__proto__ ){return false;} //如果 obj 的 __proto__ 属性不存在，也应该返回false
    return (obj.__proto__ === constructor.prototype) || isInstanceof(obj.__proto__, constructor);
}
```
# 基本类型
上面的函数已经可以实现基本功能了，但是在判断六大基本类型时会出现问题。因为我们是可以访问到一个基本类型的 \__proto___ 属性的.
```js
console.log('s'.__proto__)  // String{...}
's' instanceof String   // false
isInstanceof('s',String);   // true
```
这时我们借用 **typeof** 来判断 obj 的类型是否为 'object' 如果不是则直接返回 false。 这时可能会想到那 null 呢？ 它的返回值可也是 'object' 啊。然而没关系，因为 null 无法通过第二条判断语句。完整代码如下
```js
function isInstanceof(obj, constructor){
    if (typeof obj !== 'object'){return false;} // 可以将三条判断语句和为一句，为了更好展示每层判断内容，这里分开来写 
    if (obj === Object.prototype){return false;} 
    if !( obj.__proto__ ){return false;} //如果 obj 的 __proto__ 属性不存在，也应该返回false
    return (obj.__proto__ === constructor.prototype) || isInstanceof(obj.__proto__, constructor);
}
```

