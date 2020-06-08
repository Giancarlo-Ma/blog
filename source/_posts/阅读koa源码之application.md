---
title: 阅读koa源码之application
date: 2019-02-14 16:45:09
categories:
- JavaScript
tags:
- koa.js
---

# 阅读Koa.js源码之application

application.js是整个koajs的入口文件，对外暴露的是一个`Application`类，下面以一段代码作为演示分析koa执行过程。
```javascript
// 首先引入koa，这个对应的Koa就是application对外暴露的Application类。
const Koa = require('koa');
const app = new Koa();

// app作为Application类的实例，拥有use，listen方法
app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```

## use实现
<!-- more -->

```javascript
use(fn) {
    if (typeof fn !== 'function') throw new TypeError('middleware must be a function!');
    if (isGeneratorFunction(fn)) {
      deprecate('Support for generators will be removed in v3. ' +
                'See the documentation for examples of how to convert old middleware ' +
                'https://github.com/koajs/koa/blob/master/docs/migration.md');
      fn = convert(fn);
    }
    debug('use %s', fn._name || fn.name || '-');
    this.middleware.push(fn);
    return this;
  }
```
use方法的参数一定要是个函数，并且如果为generator函数，此处会利用convert函数做转换，但是koa 3就不再支持generator函数了。此外application实例拥有一个middleware数组，每当我们调用app.use方法，就会将当前中间件函数push进这个application实例的middleware数组，最后我们返回this实例，因此我们可以链式在application实例上调用方法。

经过分析知，这一步我们只是在application的实例的middleware数组属性上增加了一个item。


## listen实现

```javascript
listen(...args) {
    debug('listen');
    const server = http.createServer(this.callback());
    return server.listen(...args);
  }
```
listen方法接受任意数量参数，利用了ES6的rest parameters特性，在函数体内作为数组体现。可以看到，listen方法不过是http.createServer(app.callback()).listen(...args)的语法糖。我们创建了一个server实例，并传递this.callback()作为请求监听器，然后通过调用server实力的listen方法，返回了一个server实例。

**TODO: compose源码阅读**
```javascript
callback() {
    // 接收context对象的函数
    const fn = compose(this.middleware);

    if (!this.listenerCount('error')) this.on('error', this.onerror);


    const handleRequest = (req, res) => {
      // 每次请求重新创建context
      // 并传入node原生的req和res对象
      const ctx = this.createContext(req, res);
      // 实际处理请求的方法，传入ctx对象以及中间件compose之后的函数
      return this.handleRequest(ctx, fn);
    };

    return handleRequest;
  }
```
`listen`方法调用了application实例的callback方法。与callback方法相关的有三个函数，分别是compose（从koa-compose包引入），createContext和handleRequest函数。compose函数接受一个中间件数组并返回一个函数，返回的这个函数接受一个context对象和一个next参数并返回一个promise。此时我们定义了一个handleRequest函数，**这个函数就是作为createServer的请求监听器参数传入的**。而这个handleRequest通过**调用createContext创建了一个ctx对象**，并将其和通过compose生成的fn传入了application实例的handleRequest方法。

```javascript
createContext(req, res) {
    // 定义的context局部变量就是合成的ctx对象，我们在中间件中访问的context对象就是这个对象
    // 首先继承了实例属性context，实例属性继承了同文件夹下context.js文件暴露的对象
    const context = Object.create(this.context);
    // 定义koa的request对象，继承自实例属性request，实例属性request继承自同文件夹下的request文件暴露的对象，建立起了koa中context和request，response对象的关联
    const request = context.request = Object.create(this.request);
    // response同理
    const response = context.response = Object.create(this.response);
    // koa的context对象和request，response对象上的app属性均为app实例
    context.app = request.app = response.app = this;
    // context和request，response对象的req属性均为原生nodejs的req对象
    context.req = request.req = response.req = req;
    // 同理
    context.res = request.res = response.res = res;
    // koa的request和response对象的ctx属性均为此处创建的context对象，也是最终合成的context对象
    request.ctx = response.ctx = context;
    // koa中request对象的response属性为koa中的response对象
    request.response = response;
    // 同理
    response.request = request;
    // context和request对象均可通过originalUrl获取到req.url，也就是不带域名协议端口号剩下的请求路径
    context.originalUrl = request.originalUrl = req.url;
    // 在context对象上设置一个state对象，用于开发中存储有用的信息
    context.state = {};
    // 最终将这个context对象返回，并供handleRequest使用
    return context;
  }
```

```javascript
handleRequest(ctx, fnMiddleware) {
    // 通过ctx.res获取到原生node的res对象
    const res = ctx.res;
    // 设置默认状态码为404
    res.statusCode = 404;
    // 定义promise中出现错误时的callback函数
    const onerror = err => ctx.onerror(err);
    // 定义处理响应的函数
    const handleResponse = () => respond(ctx);
    // 当res发生错误时调用onerror回调
    onFinished(res, onerror);
    // 调用compose之后的fn函数并传入ctx对象，返回的是个promise，调用其then方法处理响应结果
    return fnMiddleware(ctx).then(handleResponse).catch(onerror);
  }
```

```javascript
/**
 * Response helper.
 */

function respond(ctx) {
  // allow bypassing koa
  // 当ctx.respond设置为false则绕过koa中的相应处理，也就是当前函数。
  // 当不想koa帮自己处理响应的时候使用，并不被koa官方推荐
  if (false === ctx.respond) return;
  // ctx的writable属性为false时，也不进行处理，该属性通过delegates委托到了koa的response对象中
  if (!ctx.writable) return;

  // 获取原生res对象，代理的response对象的body属性和status属性，这两个属性可以读写
  const res = ctx.res;
  let body = ctx.body;
  const code = ctx.status;

  // ignore body
  // statuses.empty是一个对象，包含204（no content），205（reset content），304（not modified）三个属性且对应值均为true
  if (statuses.empty[code]) {
    // strip headers
    // 将响应体设置为body
    ctx.body = null;
    // 标志响应结束
    return res.end();
  }

  if ('HEAD' == ctx.method) {
    if (!res.headersSent && isJSON(body)) {
      ctx.length = Buffer.byteLength(JSON.stringify(body));
    }
    return res.end();
  }

  // status body
  if (null == body) {
    if (ctx.req.httpVersionMajor >= 2) {
      body = String(code);
    } else {
      body = ctx.message || String(code);
    }
    if (!res.headersSent) {
      ctx.type = 'text';
      ctx.length = Buffer.byteLength(body);
    }
    return res.end(body);
  }

  // responses
  if (Buffer.isBuffer(body)) return res.end(body);
  if ('string' == typeof body) return res.end(body);
  if (body instanceof Stream) return body.pipe(res);

  // body: json
  body = JSON.stringify(body);
  if (!res.headersSent) {
    ctx.length = Buffer.byteLength(body);
  }
  res.end(body);
}
```