---
title: 初识flex布局
date: 2019-03-01 10:56:29
categories:
- CSS
tags:
- flex
- layout
---

# 初识flex布局

flex布局中分为container元素和item元素两大部分，这两大部分分别有自己所能控制的一些属性，列举如下：

## container

将container元素设置为display:flex即可使之内部呈现flex布局，设置为display:flex的container有如下属性：
- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content

<!-- more -->

### flex-direction

该属性指明了容器想要向哪个方向堆叠item，可以指定row，row-reverse，column，column-reverse值。默认从左向右堆叠，也就是row值。

### flex-wrap

指明flex的item是否在必要的时候换行，有三个值可选，分别是wrap，nowrap和wrap-reverse，默认为nowrap，在任何情况下都不换行。wrap-reverse表明可以换行，但是原来在第一行的item变为最后一行，也就是垂直方向上和原来的次序相反。

### flex-flow

是flex-direction和flex-wrap属性的缩写，如flex-flow: row wrap;

### justify-content

justify本身有两端对齐的意思，在这里这个属性用来在水平方向对齐item。包含五个值，分别是center，flex-start，flex-end，space-around和space-between。默认值为flex-start，从左向右的语言体系中，是左对齐。

其中space-around和space-between比较类似，space-around的效果是每个item左右都有一定的空间，而space-between的两端空间比较窄，而中间空间较大。

### align-items

用于在垂直方向上对齐高度不同的item，可以设置如下几种值：center，flex-start，flex-end，stretch和baseline。默认值为stretch，表明在垂直方向上拉伸item填满整个容器。

### align-content

用于对齐flex行，这也是一个垂直方向上的控制，可以设置如下值：center，flex-start，flex-end，stretch，space-around和space-between。默认为stretch，拉伸填满整个行。

align-items控制单行各item垂直方向对齐，align-content控制整体的items在容器垂直方向的对齐方式。

### item

item就是flex容器中一个个的子元素，可以设置如下属性：

- order
- flex-grow
- flex-shrink
- flex-basis
- flex
- align-self

### order

用于控制item显示的次序，必须为一个数字，默认为0，按html出现次序排序

### flex-grow

用于控制一个item相对于其他的item可以拉伸到多长，也是一个数值，默认为0。

### flex-shrink

用于控制一个item相对于其他的item，会缩小到多短，默认为1。表明合适情况下会收缩，而将其设置为0可以使得其他item缩小的情况下它保证不被收缩。

### flex-basis

指明item的初始长度，可以设置为像素，也可以设置为百分比。百分比相对于父容器宽度，可以利用百分比做响应式布局。

### flex

为flex-grow，flex-shrink和flex-basis三者的简写形式。

### align-self

覆盖到容器的align-items属性，指明该item在同一行的item中的对齐方式，值可以是flex-start，flex-end和center等。


# 布局案例

一个响应式布局案例

```html
<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
* {
  box-sizing: border-box;
}

/* Style the body */
body {
  font-family: Arial;
  margin: 0;
}

/* Header/logo Title */
.header {
  padding: 60px;
  text-align: center;
  background: #1abc9c;
  color: white;
}

/* Style the top navigation bar */
.navbar {
  display: flex;
  background-color: #333;
}

/* Style the navigation bar links */
.navbar a {
  color: white;
  padding: 14px 20px;
  text-decoration: none;
  text-align: center;
}

/* Change color on hover */
.navbar a:hover {
  background-color: #ddd;
  color: black;
}

/* Column container */
.row {  
  display: flex;
  flex-wrap: wrap;
}

/* Create two unequal columns that sits next to each other */
/* Sidebar/left column */
.side {
  flex: 30%;
  background-color: #f1f1f1;
  padding: 20px;
}

/* Main column */
.main {
  flex: 70%;
  background-color: white;
  padding: 20px;
}

/* Fake image, just for this example */
.fakeimg {
  background-color: #aaa;
  width: 100%;
  padding: 20px;
}

/* Footer */
.footer {
  padding: 20px;
  text-align: center;
  background: #ddd;
}

/* Responsive layout - when the screen is less than 700px wide, make the two columns stack on top of each other instead of next to each other */
@media screen and (max-width: 700px) {
  .row, .navbar {   
    flex-direction: column;
  }
}
</style>
</head>
<body>

<!-- Note -->
<div style="background:yellow;padding:5px">
  <h4 style="text-align:center">Resize the browser window to see the responsive effect.</h4>
</div>

<!-- Header -->
<div class="header">
  <h1>My Website</h1>
  <p>With a <b>flexible</b> layout.</p>
</div>

<!-- Navigation Bar -->
<div class="navbar">
  <a href="#">Link</a>
  <a href="#">Link</a>
  <a href="#">Link</a>
  <a href="#">Link</a>
</div>

<!-- The flexible grid (content) -->
<div class="row">
  <div class="side">
    <h2>About Me</h2>
    <h5>Photo of me:</h5>
    <div class="fakeimg" style="height:200px;">Image</div>
    <p>Some text about me in culpa qui officia deserunt mollit anim..</p>
    <h3>More Text</h3>
    <p>Lorem ipsum dolor sit ame.</p>
    <div class="fakeimg" style="height:60px;">Image</div><br>
    <div class="fakeimg" style="height:60px;">Image</div><br>
    <div class="fakeimg" style="height:60px;">Image</div>
  </div>
  <div class="main">
    <h2>TITLE HEADING</h2>
    <h5>Title description, Dec 7, 2017</h5>
    <div class="fakeimg" style="height:200px;">Image</div>
    <p>Some text..</p>
    <p>Sunt in culpa qui officia deserunt mollit anim id est laborum consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco.</p>
    <br>
    <h2>TITLE HEADING</h2>
    <h5>Title description, Sep 2, 2017</h5>
    <div class="fakeimg" style="height:200px;">Image</div>
    <p>Some text..</p>
    <p>Sunt in culpa qui officia deserunt mollit anim id est laborum consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco.</p>
  </div>
</div>

<!-- Footer -->
<div class="footer">
  <h2>Footer</h2>
</div>

</body>
</html>
```

## 三栏自适应布局解决方案之flex

两侧定宽和中间自适应

```html
<section class="layout flexbox">
        <style>
            .layout.flexbox .left-center-right{
                display: flex;
            }
            .layout.flexbox .left {
                width: 300px;
                background: red;
            }
            .layout.flexbox .center {
                background: yellow;
                flex: 1;
            }
            .layout.flexbox .right {
                width: 300px;
                background: blue;
            }
        </style>
        <h1>三栏布局</h1>
        <article class="left-center-right">
            
            <div class="center" style="order: 2">
                <h2>flexbox解决方案</h2>
                1.这是三栏布局的浮动解决方案； 2.这是三栏布局的浮动解决方案； 3.这是三栏布局的浮动解决方案； 4.这是三栏布局的浮动解决方案； 5.这是三栏布局的浮动解决方案； 6.这是三栏布局的浮动解决方案；
            </div>
          <div class="left" style="order: 1"></div>
            <div class="right" style="order: 3"></div>
        </article>
    </section>
```

基本思路是设置container的display为flex，然后为定宽的item设置宽度，而自适应的区域设置flex属性为1，要知道flex是flex-grow，flex-shrink和flex-basis的缩写，这里我们设置的flex为1和设置flex-grow为1是一样的。因为默认的其他的item的flex-grow值为0，所以中间的item相对于其他的item会尽可能的拉伸就实现了基本效果。然后我们利用order让center的DOM结构在最上面，优先渲染。