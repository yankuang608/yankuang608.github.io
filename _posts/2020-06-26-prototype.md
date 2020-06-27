---
layout: detail
title: 原生 JS 之原型与原型链
description: 一步步动手探索的角度带你深刻地认识原型和原型链
tags: 原生JS 原型
category: front_end
img_url: /JS.png
img_1: /prototype_img_1.png
img_2: /prototype_img_2.png
img_3: /prototype_img_3.png
img_4: /prototype.png
---
# {{ page.title }}
## 构造函数
记得我刚学JS时不是从红宝书入手，而是简单浏览了下 ES6 的基本语法便开始了用 JS 刷 leetcode 的不归路。因为当时正好有巩固下算法准备和学习 JS 两种需求，所以想到实践是学习语言最好的方式。前面确实还好，觉得JS和其他语言没什么太多差别，印象最深的还是let, var, const的区别。 但当我刷到二叉树看到下面这段代码的时候整个人蒙蔽了许久。
```js
function Node(val){
    this.val = val;
    this.left = null;
    this.right = null;
}

let root = new Node(1);
```
结合 new 这个关键字我猜到 node() 肯定是一个构造函数但是他为什么没有定义在 class 内而是一个普通的函数声明，因为当时 ES6 已经支持下面这种方式声明构造函数了
```js
class Node{
    constructor(val){
        this.val = val;
        this.left = null;
        this.right = null;
    }
}
```
后来我才知道 JS 中构造函数的声明和普通函数没有区别，或者说几乎所有函数都可以作为构造函数，因为它们都有 prototype 属性。而在说 prototype 之前就不得不先提 \__proto__ 了。
## \__proto__
在谷歌浏览器的控制台中我们创建了一个名为 godzilla 的对象，它有两个属性 weight 和 height 。然后我们查看它时发现除了定义的两个属性外还有一个 \__proto__ 的属性。并且我们注意到在控制台中输入 godzilla. 时，会有很多其他自动提示的函数例如 hasOwnProperty()，那么这些函数又是在那里定义的呢。
![Image]({{ page.img_1 }})
答案是在JS中除了顶层对象 Object 之外所有的对象都有 \__proto__ 属性指向另一个对象。 当我们试图访问一个对象的属性时，则会沿着 \__proto__ 由下向上查找直到顶层 Object ，而这条由 \__proto__ 构成的查询链接也就是**原型链**。因为查找是从下往上的，如果我们在 godzilla 中也定义了一个 hasOwnProperty 的属性，那么它会屏蔽掉 Object 中的这个同名属性。此外 hasOwnProperty() 这个函数本身也和原型链有关，它可以用来判断某个属性是否为该对象自己所有。值得一提的是，\__proto__ 是大多浏览器所支持的非规范方法, 更推荐用 Object.getPrototype() 来获取一个对象的原型。 
![Image]({{ page.img_2}})
## prototype
讲完 \__proto__ 后要理解函数的 prototype 就简单很多了。除了以下情况，如 bind() 所构建的函数，箭头函数 还有一些内置的函数(eg: Math.sin )之外，其它函数都有 prototype 属性。 当我们使用这些函数来构造一个新的实例时，实例的 \__proto__ 属性指向构造函数的 prototype 属性。 这样，所有由该构造函数实例化的对象都可以访问到该函数的 prototype 属性，所以我们可以在 prototype 中定义希望所有实例都“继承“属性，来避免重复和冗余。在其他语言语言中，这些定义在 prototype 中的属性也被称做类的成员(变量、方法)。如果还是不太明白，可以多琢磨琢磨下面这张图。
![Image]({{ page.img_4}})
## class
再回到前面声明构造函数的第二种方式, 其实是换汤不换药，依然是使用原型链，这点可以用如下代码证明。
![Image]({{ page.img_3}})
