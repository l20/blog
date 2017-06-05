---
layout: post
cover: 'assets/images/cover7.jpg'
title: 笔试经历
date:   2017-05-16 10:18:00
tags: IT
subclass: 'post tag-test tag-content'
categories: 'casper'
navigation: True
logo: 'assets/images/ghost.png'
---



下面是我的笔试所经历是试题，面完试自我感觉良好，可是一回来重做一遍想撞墙，记录下来当做爬坑经历,最主要是js的问题,基础不行还是无缘加入这家公司了。

##### 1.以下代码会输出什么？

```js
    function person(name){
     if (name) 
        this.name = name;
     console.log(this.name);
    }

    person.prototype.name = 'Tom';

    var human = {
        person : person,
        name : 'Cat'
    }

    person()            // ''
    person('Jack')      //Jack
    new person()        //Tom
    new person('Rose')  //Rose
    human.person()      //Cat
    person.call(window) //Jack

```

这里是要考察函数```this```指向的问题，分析之前先搞清楚函数```this```的指向：
> 情况1：如果一个函数中有this，但是它没有被上一级的对象所调用，那么this指向的就是window。

> 情况2：如果一个函数中有this，这个函数有被上一级的对象所调用，那么this指向的就是上一级的对象。

> 情况3：如果一个函数中有this，这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，this指向的也只是它上一级的对象。

摘自博客[http://www.cnblogs.com/pssp/p/5216085.html](http://www.cnblogs.com/pssp/p/5216085.html)

之后来看
```person() ``` 会输出空,当做普通函数执行此时```this.name```为空无异议。
```person('Jack')``` 这里输出 ```Jack``` 也无异议，当做普通函数来传参并执行，不过需要注意的是这里当做普通函数来执行之后```this```指向了全局浏览器中的```window```对象，此时的```this.name```相当于```window.name```。
```new person() //Tom```,加了```new```之后的情况，先来了解下```new```到底干了什么？

``` js
    var obj  = {}; 
    obj.__proto__ = Base.prototype;
    Base.call(obj);  
```    
+ 第一行，我们创建了一个空对象obj。
+ 第二行，我们将这个空对象的__proto__成员指向了Base函数对象prototype成员对象。
+ 第三行，我们将Base函数对象的this指针替换成obj，然后再调用Base函数，于是我们就给obj对象赋值了一个id成员变量，这个成员变量的值是”base”。

由此，通过```new```之后创建了一个指向```person```的对象，在执行到``` console.log(this.name)```后js解释器会现在本地（person的上下文）查找有关```name```属性，但是没有找到，又去原型链中查找，终于发现了个```name```属性，所以就引用了这个属性的值。
```new person('Rose')  //Rose```自然就不用解释了。
```human.person()      //Cat```，函数的```this```指向调用它的上下文。

##### 2.以下代码会输出什么？

```js
    var str = 'hello';

    function hello(str){
        var str;
        console.log(str);
    }

    hello('kitty')      // kitty
    console.log(str)    // hello
```

以上，创建上下文的时候会提升所有的变量声明和函数声明，会在这里找变量发现```function (str)```这里有个```str```变量先提升，给个默认值```undefined```然后发现没有别的变量了,里面的str由于跟参数列表里的str是同名的，所以只会提升一次，不会覆盖
而在执行的时候，var str这个事函数声明，它已经在创建执行上下文的时候提升了，所以直接略过



##### 3.以下代码会输出什么？

```js
    var a = window.a = 'hello';

    function hello(a){
        console.log(a)
        var a = 'kitty';
        console.log(a);
    }
    hello(); // undefined
             // kitty
```

##### 4.以下代码会输出什么？

```js
    function T() {
        this.name = 't';
    }

    function B() {
        this.name = 'b'
    }


    var t = new T();
    T.prototype = B.prototype;

    console.log(t instanceof T); // fasle
    console.log(t instanceof B); // fasle

```

##### 5.以下代码会输出什么？

```js
    var obj = {
        bar : 1,
        foo : 2,
        baz : 3
    }

    obj.bar = undefined;
    obj.foo = null;
    delete obj.baz;

    for (var i in obj) {
        if (obj.hasOwnProperty(i)) {
            console.log(i);
        }
    }

    //bar
    //foo
```

##### 6.以下代码会输出什么？

```js
        
    function Dog() {}

    Dog.prototype.name = 'dog'

    var d = new Dog();
     
    console.log(d.name);  // dog

    Dog.prototype.name = 'hotdog';

    console.log(d.name); // hotdog

    d.name = 'baddog';

    console.log(d.name); // baddog

    delete d.name;

    console.log(d.name); // undefined


```
