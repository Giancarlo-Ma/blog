---
title: EJ2
date: 2018-12-19 20:11:50
categories:
- Eloquent JavaScript
---

# 程序结构

## 表达式和语句

一个产生值的代码片段叫做表达式，字面量也可以看作是一种表达式，表达式还可以嵌套表达式构成新的表达式。如果将表达式比喻为句子片段，那么语句就是能表达完整意思的一句话。语句的组合构成了一个程序，程序的意义在于改变“世界”，要么可以在屏幕上打印点什么东西，要么改变某种状态，以影响后面的程序，否则，程序将是无意义的。

<!-- more -->

虽然JS的语句末尾分号有时可以省略，但建议暂时不要省略末尾分号，否则可能有奇怪的事情发生。

## 绑定

为了保存每个值，JS提供了绑定的机制，用关键字`let`声明绑定，`let one = 1;`也就是变量用于保存某种状态。变量的值不是一成不变，在程序的任何地方都可能会被改变，将绑定也就是变量想象为章鱼的触手，而不是盒子。如果请求一个未初始化的变量，将会得到underfined。

单个`let`语句也可以声明多个绑定，用逗号隔开即可。如`let one = 1, two = 2;`。

关键字`var`和`const`也用于变量声明。如：
```javascript
var name = "Ayda";
const greeting = "Hello ";
console.log(greeting + name);
// -> Hello Ayda
```
关键字`var`用于pre-2015 JavaScript。下一章再讲述它和let的区别，这本书中主要用let，var有一些令人疑惑的特征。

`const`关键字用于常量声明，只要变量不被销毁就保持相同的值不变。

## 绑定名（变量名）

变量名可以是任何单词，包括数字，但不可以用数字开头。可以包括`$`和`_`，字母，数字但不能包含其他标点符号或特殊字符。

关键字和保留字不可以被用于变量名，不需要特殊记忆，出现错误时再查询即可。

## 环境

在某个特定时间，变量和它们的值的集合叫做environment（环境）。程序开始时，环境并不为空，因为包含一些语言标准的API，还提供了一些和周围系统做交互的方式。比如浏览器端，可以和当前加载的网站做交互或者读取鼠标键盘的输入。

## 函数

许多被提供在默认环境里面的值都是函数（function）类型。function就是一段代码被包装在一个值中。这样的值可被调用去运行包装的程序。

例如浏览器环境下的变量`prompt`绑定着一个函数，他显示一个对话框请求用户输入。

执行一个function被叫做invoking、calling或者applying it。可以通过在产生函数值得变量名后面加一个括号调用函数，括号中的内容就是参数（arguments），不同函数可能需要不同类型不同数量的参数。

`prompt`函数并不是用的很多，但是可以用于实验目的或者玩具程序。

## console.log函数

对于JS的两种主要环境，浏览器端和Node端，都可以使用console.log向文本输出设备打印值。浏览器端使用F12调用开发者工具，在控制台就可以使用该函数。

前面说过变量名不可包含`.`号，但是这里为什么有呢？是因为这不是一个简单的变量，实际上是个表达式检索console的log属性。第四章会详细说明。

## 返回值

展示对话框或者打印什么东西属于副作用，需要函数有用在他们的副作用。函数也可以接受参数，产生值，在这种情况下，不需要副作用。比如，Math的max方法。
```javascript
console.log(Math.max(2, 3));
// -> 3
```

一个函数产生值，被叫做返回那个值。JS中，产生值的都是表达式，所以函数调用也可以被用于更大的表达式，如
```javascript
console.log(Math.min(2, 4) + 100);
// → 102
```

## 控制流

当程序多于一条语句，从上向下依次执行。如：
```javascript
let theNumber = Number(prompt("Pick a number"));
console.log("Your number is the square root of " +
            theNumber * theNumber);
```

Number函数转换一个值为数字，prompt产生字符串，所以需要做转换，紧接着的一行打印输入数字的平方数字。还有类似的函数如String和Boolean。这是最简单的直线控制流。

## 条件性执行

有时，我们想让程序按照分支执行，比如我们只想让输入是数字的才计算它的平方数。如：
```javascript
let theNumber = Number(prompt("Pick a number"));
if (!Number.isNaN(theNumber)) {
  console.log("Your number is the square root of " +
              theNumber * theNumber);
}
```
在这种情况下，如果输入了非数字类型的值，什么也不会被输出。

Number.isNaN是标准JS函数只有在参数为NaN的时候才返回true。

大括号包围起来的部分叫做一个block（块），如果if后面只有一条语句，可以省略大括号，如
```javascript
if (1 + 1 == 2) console.log("It's true");
// → It's true
```

也可以用`else`表示替代分支。如
```javascript
let theNumber = Number(prompt("Pick a number"));
if (!Number.isNaN(theNumber)) {
  console.log("Your number is the square root of " +
              theNumber * theNumber);
} else {
  console.log("Hey. Why didn't you give me a number?");
}
```

如有多条路径可供选择，可将多个if/else链在一起。如：
```javascript
let num = Number(prompt("Pick a number"));

if (num < 10) {
  console.log("Small");
} else if (num < 100) {
  console.log("Medium");
} else {
  console.log("Large");
}
```

## while和do-while循环

计算机最厉害的地方就在于循环，有了循环，我们可以实现很多自动化的事情。如while循环：
```javascript
let number = 0;
while (number <= 12) {
  console.log(number);
  number = number + 2;
}
// → 0
// → 2
//   … etcetera
```
上面打印0到12的所有偶数。又如：
```javascript
let result = 1;
let counter = 0;
while (counter < 10) {
  result = result * 2;
  counter = counter + 1;
}
console.log(result);
// → 1024
```
计算2的10次方，都是很简单的程序。

do-while先执行循环体在判断条件：
```javascript
let yourName;
do {
  yourName = prompt("Who are you?");
} while (!yourName);
console.log(yourName);
```

逻辑非会将prompt返回的字符串值转为布尔值，字符串中，除了空字符串在转化成布尔值的时候返回false，其余都是true，也就意味着上面的程序会持续询问yourName直到yourName不是空字符串为止。

## 缩进代码

```javascript
if (false != true) {
  console.log("That makes sense.");
  if (1 < 2) {
    console.log("No surprise there.");
  }
}
```

良好的缩进保证代码可读性。

## for循环

```javascript
for (let number = 0; number <= 12; number = number + 2) {
  console.log(number);
}
// → 0
// → 2
//   … etcetera
```
for循环和while循环一样，只不过把常用的循环结构抽象为更简洁的结构。如：
```javascript
let result = 1;
for (let counter = 0; counter < 10; counter = counter + 1) {
  result = result * 2;
}
console.log(result);
// → 1024
```
同样包含初始化，判断条件和迭代过后对状态的改变。

## 跳出循环

除了判断条件为false外，还有一种机制跳出循环，就是`break`关键字。
```javascript
for (let current = 20; ; current = current + 1) {
  if (current % 7 == 0) {
    console.log(current);
    break;
  }
}
// → 21
```
大于等于20并且是7的倍数。

还有一个关键字`continue`跳过当前循环直接进入下一次迭代。

## 简洁改变变量

就是++，--，+=，-=，*=,/=等的运用，不赘述。

## 用switch结构

```javascript
if (x == "value1") action1();
else if (x == "value2") action2();
else if (x == "value3") action3();
else defaultAction();
```

```javascript
switch (prompt("What is the weather like?")) {
  case "rainy":
    console.log("Remember to bring an umbrella.");
    break;
  case "sunny":
    console.log("Dress lightly.");
  case "cloudy":
    console.log("Go outside.");
    break;
  default:
    console.log("Unknown weather type!");
    break;
}
```

遇到break才会停止。

## 大写

变量名不许有空格，但是用多个单词表达含义是有益的。
```
fuzzylittleturtle
fuzzy_little_turtle
FuzzyLittleTurtle
fuzzyLittleTurtle
```
JS采用最后一种驼峰式命名变量的方法。

## 注释

```javascript
let accountBalance = calculateBalance(account);
// It's a green hollow where a river sings
accountBalance.adjust();
// Madly catching white tatters in the grass.
let report = new Report();
// Where the sun on the proud mountain rings:
addToReport(accountBalance, report);
// It's a little valley, foaming like light in a glass.
```
// 单行注释
/* */ 多行注释

```javascript
/*
  I first found this number scrawled on the back of an old notebook.
  Since then, it has often dropped by, showing up in phone numbers
  and the serial numbers of products that I've bought. It obviously
  likes me, so I've decided to keep it.
*/
const myNumber = 11213;
```

## 练习

### 循环三角形

```javascript
for(let i = 1; i <= 7; i++) 
    let temp = "";
    for(let j = 0; j < i; j++) 
        temp += "#";
    console.log(temp + "/n");
```
### FIZZBUZZ

```javascript
for(int i = 1; i <= 100; i++) {
    if(i % 3 == 0 && i % 5 == 0) console.log("FizzBuzz");
    if(i % 3 == 0) console.log('Fizz');
    else if(i % 5 == 0) console.log("Buzz");
    else console.log(i);
}
```
### 棋盘

```javascript
let flag = 0;
for(int i = 0; i < size; i++) {
    let s = "";
    for(int j = 0; j < size; j++) {
        if((i + j) % 2 == 0) s += " ";
        else if((i + j) % 2 == 1) s += "#"; 
    }
    console.log(s + "/n");
}
```