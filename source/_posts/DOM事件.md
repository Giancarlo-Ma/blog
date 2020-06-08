---
title: DOM
date: 2018/01/12 20:46:25
categories:
- JavaScript
tags:
- JavaScript
---

这一章主要学习DOM事件相关知识
- 基本概念：DOM事件级别
- DOM事件模型
- DOM事件流
- 描述DOM事件捕获的具体流程
- Event对象的常见应用
- 自定义事件

<!-- more -->

------

### DOM节点

整个文档都是由不同的节点组成的，共有12种不同的节点类型，通过nodeType可以查看节点的类型，但我们经常使用的只有四种。元素为1，文本为3，注释为8，文档（document）为9。（空白折行会产生文本节点。）


节点名称通过nodeName属性获取，
---

### DOM事件的级别

- DOM0：element.onclick=function(){}

- DOM2: element.addEventListener('click',function(){},false)

  第三个参数表明是在捕获阶段还是冒泡阶段触发，如果是false也就是默认情况下是在冒泡阶段触发，如果是true就是在捕获阶段触发。

- DOM3: element.addEventListener('keyup',function(){},false)

    主要是新增了一些鼠标键盘事件

---

### 事件模型

- 事件捕获：自上而下

- 事件冒泡：自下而上

---



### 事件流

事件是如何传播到页面的？首先事件通过捕获到达目标元素，目标元素再上传到window对象。

---

### DOM捕获的流程
(document.body可以获取body元素，但是html元素要通过document.documentElement)
window -> document -> html -> body...->目标元素

冒泡流程刚好相反

---

### Event对象

- event.preventDefault()
  阻止默认行为

- event.stopPropagation()
  阻止冒泡，子元素绑定的事件会冒泡到父元素，有的时候我们不需要这种效果，就需要阻止冒泡。

- event.stopImmediatePropagation()
  在一个元素上绑定两个事件响应函数的时候，可以避免另一个事件的响应

- event.currentTarget

  是指注册了事件监听器的对象，是target的父级对象，比如我们点击的时候，父级元素注册了事件监听器，在事件冒泡的过程中，currentTarget就可能是父级注册了事件监听器的那个元素。

- event.target

  多个子元素的事件绑定到父级元素上，即为事件代理，通过target获取当前点击的子元素

---

### DOM操作

- css样式：元素.style.cssName = cssValue;
- 获取元素：document.getElementById(id)、document.getElementsByTagName(tagName);
- 添加自定义属性：setAttribute(name,value);
- 获取自定义属性：getAttribute(name);
- 获取标准属性：元素打点调用;


---

### 自定义事件（模拟事件）

```javascript
var eve = new Event('custom');
ev.addEventListener('custom',function(){
  console.log('custom');
})
// 派发事件
ev.dispatchEvent(eve);
```

CustomEvent对象还可以指定参数

---

------


2018 年 1月 12日
