---
title: 阅读delegates源码
date: 2019-02-14 22:22:08
categories:
- JavaScript
tags:
- Koa.js
---

# delegates

因为Koa.js在将request对象和response对象的属性委托到context对象时，依赖了delegates包，我们就来分析一下这个包

```javascript
module.exports = Delegator;

/**
 * Initialize a delegator.
 *
 * @param {Object} proto
 * @param {String} target
 * @api public
 */
  
function Delegator(proto, target) {
  // 只有在new Delefator的时候this才会指向新创建的对象
  // 若不是以new方式创建对象，则返回一个新的delegator实例
  // 接着就继续执行构造函数
  if (!(this instanceof Delegator)) return new Delegator(proto, target);
  this.proto = proto;
  this.target = target;
  this.methods = [];
  this.getters = [];
  this.setters = [];
  this.fluents = [];
}
```

delegates包对外暴露一个函数，其本质为一个构造函数，作用也是作为类存在的，我们通过暴露的构造函数可以实例化一个delegator。Koa.js中在context.js文件中利用了delegates将context对象上的某些getter，setter和方法委托给了context.request以及context.response对象。

<!-- more -->

## method方法

```javascript
/**
 * Delegate method `name`.
 *
 * @param {String} name
 * @return {Delegator} self
 * @api public
 */  

Delegator.prototype.method = function(name){
  var proto = this.proto;
  var target = this.target;
  this.methods.push(name);

  proto[name] = function(){
    return this[target][name].apply(this[target], arguments);
  };

  return this;
};
```

**注意到delegator实例的每个方法都返回了this，意味着可以以链式调用的方法调用delegator实例的相关方法。**

method方法的重点在第四句，我们在将proto对象的name属性定义为一个函数，而该函数的调用会被委托给proto对象的target属性上的name属性。这里可能会比较绕，但其实不难，问题就在于我们要理解函数中的this指向的问题。前面三行的this指向的是当前的delegator实例，而我们在proto对象上定义name属性时，后面这个函数中的this和delegator实例就没有什么关系了。因为这个函数中的this的含义取决于这个函数是如何调用的。

联想到Koa中我们可以直接使用类似`ctx.body`或者`ctx.body=`或者`ctx.redirect()`这样的形式。其实这里并不是因为ctx对象本身就有body getter和setter或者有redirect这样的方法，而是因为我们在context.js文件中进行了对应request对象和response对象上面的getter和setter方法委托。

我们首先要理解proto[name]之后的函数是怎么调用的，才能知道它的this指向，我们在实际调用时是通过proto这个对象对它的name属性进行方法调用，对应于koa中就是ctx这个对象，那么函数体中的this就自然指向了ctx对象，我们函数体的意图就是找到ctx的target上面的name属性，来调用它的方法并返回它执行的结果。这里我们还利用了apply来对所调用方法中this的设置，将其中的this设置为了对应的target对象。

理解了method方法，后面的其他方面就是一个道理了。简单说来，就是我们首先要在proto这个对象上先设置一个对应名字的方法或者getter和setter，然后在这个函数体中，我们去调用代理对象的相关方法或者getter和setter。


## getter，setter和access方法

这三个方法相互关联，放在一起分析。

```javascript
Delegator.prototype.getter = function(name){
  var proto = this.proto;
  var target = this.target;
  this.getters.push(name);

  proto.__defineGetter__(name, function(){
    return this[target][name];
  });

  return this;
};

Delegator.prototype.setter = function(name){
  var proto = this.proto;
  var target = this.target;
  this.setters.push(name);

  proto.__defineSetter__(name, function(val){
    return this[target][name] = val;
  });

  return this;
};

Delegator.prototype.access = function(name){
  return this.getter(name).setter(name);
};
```

`__defineSetter__`和`__defineGetter`是已经不被推荐的设置对象中get和set方法的一种遗留方式。我们在proto对象上这次定义get和set方法，而在对应的get和set方法里，我们通过get属性和set属性调用对应的代理对象的get或者set方法。

而access则是既代理getter方法又代理setter方法。

## fluent方法

```javascript
Delegator.prototype.fluent = function (name) {
  var proto = this.proto;
  var target = this.target;
  this.fluents.push(name);

  proto[name] = function(val){
    if ('undefined' != typeof val) {
      this[target][name] = val;
      return this;
    } else {
      return this[target][name];
    }
  };

  return this;
};
```
和method类似，依然是在proto对象上设置name属性为一个方法，如果我们调用该方法时没有传入参数，则返回代理对象上的对应属性，如果传入了参数，则将代理对象的对应属性设置为传入的值。