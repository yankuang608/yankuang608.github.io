---
layout: detail
title: apply 和 call 的模拟实现
description: 对比实现两张用来设置 this 指针的方法
tags: 原生JS apply call
category: front_end
img_url: /JS.png
---
# {{ page.title }}
## 分析
apply 和 call 都可以实现指定函数运行时的 this，区别在于 apply 将参数放入一个数组内传递，两者都包含以下两个步骤。
1. 绑定 this
2. 传入参数并执行函数

为了实现第一点，可以考虑将函数作为属性添加到指定的 context 中，这样当执行函数时，函数的 this 就会指向 context ，在执行完成后再删除。 而第二点可以通过 eval() 函数来执行。 eval() 会将传入的字符串以 JS 代码的方式运行。此外，如果 context 为 null
this 会默认指向 window;
```js
Function.prototype.myCall = function(context){
    // 这里我们不用slice，因为slice也调用了call()方法Array.prototype.slice.call()
    context = context || Window;
    let args = [];
    for (let i = 1 ; i < arguments.length; i++){
        args.push('argumets[' + i + ']');
    }
    context.fn = this;
    // args会自动调用 Array.toString() 函数，也就是会将 [1,2,3] 转换成 "1,2,3"
    let result = eval('context.fn(' + args + ')');
    delete context.fn;
    return result;
}

let obj = {name:'godzilla'};
let name = 'kingkong'
function say(word){console.log(this.name + word)}
say.call(obj,' is the king');  // godzilla is the king
say.call(null,' is the king');  // kingkong is the king
```
## apply
apply 函数的实现类似，直接贴上代码
```js
Function.prototype.myApply = function(context,arr){
    // 这里我们不用slice，因为slice也调用了call()方法Array.prototype.slice.call()
    context = context || Window;
    context.fn = this;
    let result, args = [];
    for (let i=0; i < arr.length; i++){
        args.push('arr[' + i + ']');
    }
    // args会自动调用 Array.toString() 函数，也就是会将 [1,2,3] 转换成 "1,2,3"
    result = eval('context.fn(' + args + ')');
    delete context.fn;
    return result;
}

let obj = {name:'godzilla'};
let name = 'kingkong'
function say(word){console.log(this.name + word)}
let arr = [' is the king'];
say.myApply(obj, arr);  // godzilla is the king
say.myApply(null, arr);  // kingkong is the king
```