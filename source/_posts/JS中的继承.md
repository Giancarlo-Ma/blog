---
title: JS中的继承
date: 2019-01-27 11:38:39
categories:
- JavaScript
tags:
- 原型链
- 继承
---

# JS中的继承

## 原型链

提到继承，不得不说的是JS中的原型链概念。JS不像传统的面向对象语言，在ES6出现以前，JS标准中并没有类的概念，所以往往是通过原型链的特性实现面向对象的继承。看过一些资料以后，发现其实原型链的概念十分简单，一句话总结：JS中的每个对象都有一个`__proto__`属性，而这个属性指向它的构造函数的`prototype`属性，而因为函数在JS中也是对象，所以函数本身也有`__proto__`属性，而在对象上进行属性查找就会沿着这条链一直向上进行，直到原型链的终点`Object.prototype`（`Object.prototype.__proto__`属性为null）。还有一个重要的点是构造函数的`prototype`对象有一个`constructor`属性，这个属性指向构造函数本身，我们可以利用这个特性建立对象和其构造函数之间的联系，因为我们知道，对于`bar.constructor`属性的查找，如果`bar`本身没有`constructor`属性，就会在其`__proto__`属性上查找，而`__proto__`又指向其构造器的`prototype`，所以依据这个特性，就能找到某对象的构造函数。另外一个便捷的判断实例构造器的方法是利用`instanceof`操作符，这个操作符会沿着对象的原型链一直向上查找直到对象的`__proto__`的`__proto__`...等于函数的`prototype`属性，或是一直到`Object.prototype`都没有相等，返回`false`。

<!-- more -->

## 实现继承

因为我们要实现类的继承，这里定义父类为`Parent`，子类为`Child`，先看一下最基本实现继承的代码：
```javascript
function Parent(name) {
    this.name = name
}

function Child(name) {
    Parent.call(this, name);
}

Child.prototype.__proto__ = Parent.prototype;
```

实现继承分为两个步骤：
- 在子类中调用父类的构造函数
- 建立原型链（继承链）

JS中的每个函数都有一个`call`方法和一个`apply`方法，这两个方法的作用都是改变调用上下文`this`的指向，我们在子类中调用`Parent.call(this, name);`就是表明我们需要调用`Parent`这个函数，并且将`Parent`构造函数中的`this`设置为`Child`中的`this`，在通过`new`操作符实例化对象时，这个`this`表现为新创建的对象，借此就实现了在子类中调用父类的构造函数。`apply`方法也用于改变函数调用的上下文，只是传入的参数以数组方式体现。

我们知道，原型链的查找规则是先从对象本身开始，然后沿着`__proto__`一直向上。我们要将两个类链接起来，就需要在对象的`__proto__`上做调整，由于`__proto__`指向构造函数的`prototype`，所以我们设置子类的构造器的`prototype`，让其`__proto__`指向父类的`prototype`，当我们在当前实例对象或者其`__proto__`上查找不到要抄找的属性或方法时，就会去查找父类的构造函数的`prototype`，所以我们就建立了父类和字类之间的联系。

## Node.js中的继承

Node.js中的`util`核心模块提供了一个`inherits`函数，这个函数封装了建立原型链的实现。传入的第一个参数为子类，传入的第二个参数为父类。

```javascript
const inherits = require('util').inherits;
function Parent(name) {
    this.name = name;
}

Parent.prototype.bar = () => console.log('我是父类的bar方法');

function Child(name) {
    Parent.call(this, name);
}

Child.prototype.foo = () => console.log('我是子类的foo方法');

inherits(Child, Parent);

let child = new Child('ann');
let parent = new Parent('bob');

child.foo(); // 我是子类的foo方法
child.bar(); // 我是父类的bar方法
```

因为`__proto__`不是JS标准的一部分，所以人为设置并不推荐。其实有一个`Object.create`方法用于根据一个对象来创造另一个对象，并且新对象的`__proto__`指向该方法传入的参数。

```javascript
let foo = {};
let bar = Object.create(foo);
console.log(bar.__proto__ === foo); // true
```

所以上面的例子就可以改写成

```javascript
function Parent(name) {
    this.name = name
}

function Child(name) {
    Parent.call(this, name);
}

Child.prototype = Object.create(Parent.prototype);
```

但是这种方法相当于`Child.prototype = {__proto__: Parent.prototype}`，因此就会丢失`Child.prototype.constructor`属性，所以我们在`Object.create`还可以传入第二个参数，该参数为一个对象，表明创建的新对象需要额外添加的属性。
```javascript
function Parent(name) {
    this.name = name
}

function Child(name) {
    Parent.call(this, name);
}

Child.prototype = Object.create(Parent.prototype, {
    constructor: {
        value: Child,
        enumerable: false,
        writable: true,
        configurable: true
    }
});
```
查看了一下最新的`nodejs`中`inherits`函数的实现，如下所示：
```javascript
function inherits(ctor, superCtor) {

  if (ctor === undefined || ctor === null)
    throw new ERR_INVALID_ARG_TYPE('ctor', 'Function', ctor);

  if (superCtor === undefined || superCtor === null)
    throw new ERR_INVALID_ARG_TYPE('superCtor', 'Function', superCtor);

  if (superCtor.prototype === undefined) {
    throw new ERR_INVALID_ARG_TYPE('superCtor.prototype',
                                   'Object', superCtor.prototype);
  }
  Object.defineProperty(ctor, 'super_', {
    value: superCtor,
    writable: true,
    configurable: true
  });
  Object.setPrototypeOf(ctor.prototype, superCtor.prototype);
}
```
关键性的一句是最后一句，我们利用了`Object.setPrototypeOf`方法直接将子类的`prototype`的`__proto__`关联到父类的`prototype`上。我们还发现在调用`inherits`函数时还将在子类的构造器上额外定义一个`super_`属性，用这个属性可以方便的找到父类构造器。并且官方文档不建议我们使用该方法，可以使用ES6内建的对类和继承的支持。

[<span style = "color: red">inherits文档</span>](https://nodejs.org/docs/latest/api/util.html#util_util_inherits_constructor_superconstructor)

曾经的nodejs采用过下面这种实现:
```javascript
exports.inherits = function(ctor, superCtor) {
    ctor.super_ = superCtor;
    ctor.prototype = Object.create(superCtor.prototype, {
        constructor: {
            value: ctor,
            enumerable: false,
            writable: true,
            configurable: true
        }
    });
};
```
