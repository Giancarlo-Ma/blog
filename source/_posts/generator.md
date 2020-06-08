---
title: generator
date: 2019-02-13 11:16:17
categories:
- JavaScript
tags:
- generator
---

# Generator函数

生成器函数作为ES6的新特性之一，已经并不是什么新鲜的概念了。在此之前，Python，Php等均有生成器的概念。其本质就是一个返回iterator的函数，通过yield关键字可以暂停函数的执行，并在下一次调用相关方法时继续函数的执行。

## 基本用法

```javascript
function* letterGenerator() {
  yield 'a';
  yield 'b';
  yield 'c';
}

const letterIterator = letterGenerator();
letterIterator.next(); // {value: 'a', done: false}
letterIterator.next(); // {value: 'b', done: false}
letterIterator.next(); // {value: 'c', done: false}
letterIterator.next(); // {value: undefined, done: true}
```

可以看到通过调用生成器函数，我们得到了一个iterator对象。iterator对象包含next方法，可用于迭代iterable类型的数据。通过调用iterator的next方法，我们得到一个包含两个属性value和done的对象，用于标识我们的迭代过程，这也是generator函数最基本的用法。

<!-- more -->

### 控制权传递

```javascript
function* controlTransfer() {
  yield 'a';
  yield* another();
  yield 'd';
}

function* another() {
  yield 'b';
  yield 'c';
}
```

和上面类似，只不过新增了`yield*`语法，通过这种方式，我们可以将generator函数的控制权转交给另外的generator函数。其实会发现，`yield*`后面跟着的应该是一个迭代器对象。

### 可以接受参数的generator

```javascript
function* acceptParamGenerator(param) {
  const word = yield 'hello ' + param;
  yield word + ' world';
}

const iterator = acceptParamGenerator('happy');
iterator.next().value; // 'hello happy'
iterator.next('hello').value; // 'hello world'
```

可以看到，我们通过这种方式可以和generator函数进行通信，我们首先调用`next`方法生成了第一个`yield`之后的内容，然后传递参数给`next`方法，其实这个参数会被yield左侧赋值的变量`word`接收。这样实现了在generator函数中的双向通讯，可以通过`yield`向外传参，也可以通过`next`向generator传参。

注意`next`方法只能给等待执行的`yield`传参，而不能给第一个`yield`传参，因为我们调用`next`方法的时候，第一个`yield`就会立刻执行。但是我们可以通过给generator函数本身传参，这样也可以实现类似的效果。

### 向generator抛出异常

```javascript
function* exceptionGenerator() {
  try {
    yield 'a';
  } catch(e) {
    console.log(e);
  }
}

const iterator = exceptionGenerator();
console.log(iterator.next().value); // 'a'
iterator.throw('catch this'); // 'catch this'
```

每一个iterator除了含有一个`next`方法之外，还有一个`throw`方法，用于向原generator函数抛出异常。
