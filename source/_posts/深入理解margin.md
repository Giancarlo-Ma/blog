---
title: 深入理解margin
date: 2019-02-28 19:28:38
categories:
- CSS
tag:
- margin
- layout
---

# 深入理解margin

## margin负值的应用

负margin可以增加元素宽度，典型应用为一列元素，每个元素都有一个margin-right，但是又不想最后那个元素的margin-right显示出来，在父元素和子元素中间增加一层元素来为它设置margin-right为负值，以此来增加父元素宽度，即可实现这种效果。

<!-- more -->

```html
<div class='box'>
  <div class='ul'>
    <div class='li'>列表1</div>
    <div class='li'>列表2</div>
    <div class='li'>列表3</div>
  </div>
</div>
```

```css
.box {
  width: 960px;
  margin: auto;
  background: orange;
}

.ul {
  overflow: hidden;
  margin-right: -10px;
}

.li {
  float: left;
  background: green;
  margin-right: 10px;
  width: 313.33px;
  height: 200px;
}
```

{% asset_image negative_margin.png %}

我们先确定item的margin-right，然后设置ul的margin-right刚好为这个值的相反数，用box元素的宽度加上这个margin-right，再减去三个margin-right的值，计算出的数值除以3，则得到每个元素的宽度数值。

### 圣杯布局

margin的百分比基于父元素计算，对于浮动元素的margin-left，如果为-100%，则会到上一行，因为走完了一整个父元素盒子，而后面的元素也会因为这个元素的移动而改变布局。

```css
.container {
    padding-left: 220px;//为左右栏腾出空间
    padding-right: 220px;
}
.left {
  float: left;
  width: 200px;
  height: 400px;
  background: red;
  margin-left: -100%;
  position: relative;
  left: -220px;
}
.center {
  float: left;
  width: 100%;
  height: 500px;
  background: yellow;
}
.right {
  float: left;
  width: 200px;
  height: 400px;
  background: blue;
  margin-left: -200px;
  position: relative;
  right: -220px;
}
```
```html
<article class="container">
  <div class="center">
    <h2>圣杯布局</h2>
  </div>
  <div class="left"></div>
  <div class="right"></div>
</article>
```

首先center，left和right都设置为左浮动，设置center宽度为100%，实现宽度自适应。如此一来，left和right就会掉在center下面，因为center占据了全部的空间。

{% asset_img shengbei1.png %}

然后将left的margin-left设置为-100%，right的margin-left设置为自身长度的相反数，就可以将left左移到上一行的起始位置，而right刚好左移到上一行的末尾

{% asset_img shengbei2.png %}

设置父容器的padding-left和padding-right，让他们给left和right腾出空间，可以稍微设置的大一点，在此设置为220px。而left和right的宽度为200px，这样我们就能再下面移动left和right到对应的位置后可以和center有20px间距。

下面我们对left和right设置相对定位，注意相对定位是相对于原来所在的位置，将left的left值设置为预留的padding的相反数，而right的right值设置为预留的padding的相反数，保证了二者向正确的方向移动。

圣杯布局有一些缺点：
- center部分的最小宽度不能小于left部分的宽度，否则会left部分掉到下一行
- 如果其中一列内容高度拉长(如下图)，其他两列的背景并不会自动填充。(借助等高布局正padding+负margin可解决，下文会介绍)


### 双飞翼布局

同样也是三栏布局，在圣杯布局基础上进一步优化，解决了圣杯布局错乱问题，实现了内容与布局的分离。而且任何一栏都可以是最高栏，不会出问题。

双飞翼布局前面的思路是一样的，先浮动三个子元素，这时left和right会掉在下面，然后我们利用margin-left的负数特性，将left和right分别推上去到合适的位置。唯一改变的就是对center的处理方式，我们在圣杯布局中是利用的container的左右padding，因为center本来是占据父容器的100%，所以就把center限制在了父容器的content区块中，即减少了content本身的宽度，而给left和right留出来空间。

在双飞翼布局中，我们不利用padding，转而使用margin，这时我们就需要在center中新增一个子元素inner，给inner设置margin，这样我们就将内容限定在了中间，而给left和right留出空间。此时我们不再需要相对定位了。因为不设置container的padding，就不会影响left和right的位置，left和right已经在正确的位置了。我们设置的margin只会影响center的显示区域。

```html
<article class="container">
    <div class="center">
        <div class="inner">双飞翼布局</div>
    </div>
    <div class="left"></div>
    <div class="right"></div>
</article>
```
```css
.container {
    min-width: 600px;//确保中间内容可以显示出来，两倍left宽+right宽
}
.left {
    float: left;
    width: 200px;
    height: 400px;
    background: red;
    margin-left: -100%;
}
.center {
    float: left;
    width: 100%;
    height: 500px;
    background: yellow;
}
.center .inner {
    margin: 0 200px; //新增部分
}
.right {
    float: left;
    width: 200px;
    height: 400px;
    background: blue;
    margin-left: -200px;
}
```

{% asset_img shuangfeiyi.png %}
