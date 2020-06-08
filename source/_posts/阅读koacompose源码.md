---
title: 阅读koa-compose源码
date: 2019-02-15 19:30:06
categories:
- JavaScript
tag:
- Koa.js
---

# koa-compose

要深入理解koa中的中间件的运行机制，必须要明白的是koa-compose这个包究竟做了些什么。koa-compose包接收一个数组，返回一个compose之后的函数，并且返回的函数接收一个ctx对象和next函数参数，并最终返回一个promise。理解koa-compose需要首先掌握递归概念以及ES6的promise和generator函数以及async/await等特性。

```javascript
function compose (middleware) {
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }

  /**
   * @param {Object} context
   * @return {Promise}
   * @api public
   */

  return function (context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```
compose这个函数在koa中是在handleRequest函数调用时传入的，在handleRequest的函数体中，调用了compose生成的结果函数fnMiddleware并且传入了合成的ctx对象，至此compose返回的函数的生命周期开始。我们进入到compose的函数体中，也就是下面这一段。

```javascript
function (context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
```
首先context为application中createContext函数返回的context对象，而next最初为undefined。

中间件序号从0开始，将index首先设置为-1，表明上次调用的中间件的序号为-1，也就是代表程序初始状态。此时返回dispatch(0)。于是我们开始调用dispatch函数并且该函数最终返回一个promise。

这里假设我们有两个中间件函数，我们首先取到第一个中间件函数并将其赋值给fn变量，判断条件不成立，继续执行，返回一个promise，但是这个resolve方法的操作数是一个fn函数调用的结果，因此我们需要调用fn函数，并且将fn函数的返回结果传递到resolve方法中。而fn函数就是我们在app.use中传入的中间件函数，于是开始调用第一个中间件函数，执行中间件函数中的代码，如果遇到了await next()，那么我们就调用传入的next方法，而中间件函数中的next参数就是我们这里传入的`dispatch.bind(null, i + 1)`函数，于是下一轮的dispatch函数就开始了它的生命周期。

此时dispatch函数中的参数index和i均为1，而相应的fn函数也变成了第二个中间件函数。类似于上面的过程，我们依然返回了一个promise对象，且由于resolve方法的参数为fn函数调用的结果，我们则首先需要调用fn函数，也就是第二个中间件函数的函数体。在此过程中，我们既可以写`await next()`，也可以不写。

假如我们写了`await next()`，那么dispatch函数会再一次调用，并且传入的i参数又加1，此时变为2。于是我们调用`dispatch(2)`。但是此时判定条件成立，于是返回了一个空的promise对象，返回之后，我们继续上一轮fn函数next调用下面的函数内容，直至执行完毕。

此时第二个中间件函数体已然执行完毕，则第一次调用的resolve方法中的参数fn函数中的next部分就执行完毕，于是开始执行next调用下面的内容了，直至函数执行完毕，resolve中的操作数计算完成，此时就可以执行application.js中then方法中的handleResponse了。

总的来说，就是通过promise控制了中间件的调用流程，形成了一种类似栈方式的中间件调用，而本质还是通过递归实现的。