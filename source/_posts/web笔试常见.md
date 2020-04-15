---
title: web笔试常见
catalog: true
date: 2020-03-13 16:52:14
subtitle:
header-img:
tags: IT
---

## javascript中的new操作符

1. 创建一个新对象；
2. 这个新对象会被执行 [[原型]] 连接；
3. 将构造函数的作用域赋值给新对象，即 this 指向这个新对象；
4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

下面实现new操作符的功能：

``` js
    function _new (fn, ...args) {
        // 1、创建一个新对象
        const obj = {}

        // 2、将这个新对象执行[[原型]]连接
        obj.__proto__ = fn.prototype

        // 3、将构造函数的作用域指向新对象
        const res = fn.apply(obj, args)

        // 4、如果构造函数返回的是‘object’或者‘function’类型则返回构造函数的返回值
        if (typeof(res) == 'object' || typeof(res) == 'function') {
            return res
        }

        // 5、否则返回这个新对象
        return obj
    }

    function Parent (name, age) {
        this.name = name
        this.age = age
    }
    
    Parent.prototype.intro = function() {
        console.log('My name is ', this.name)
        console.log('I am', this.age, 'years old.')
    }

    const p = _new(Parent, 'Alan', 100)
    p.intro()
```