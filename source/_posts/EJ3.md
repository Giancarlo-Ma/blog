---
title: EJ3
date: 2018-12-19 18:39:03
categories:
- Eloquent JavaScript
---

# Functions

JS中的函数可以包装在一个变量当中，也就是说，JS中的函数和其他诸如数字，字符串类型别无二致，只是函数会有一些额外的特性。函数的存在使我们可以更好实现复用，使我们丰富我们的词汇表。

<!-- more -->

## 定义函数

函数定义就是常规的变量声明，只是变量的值是一个函数。如：
```javascript
const square = function(x) {
  return x * x;
};

console.log(square(12));
// → 144
```

函数需要用一个function开头的表达式创建。函数包含一些参数和函数体，函数体被调用的时候，内部语句按顺序执行，函数体要包裹在大括号内部。

函数可以没有参数或有多个参数
```javascript
const makeNoise = function() {
  console.log("Pling!");
};

makeNoise();
// → Pling!

const power = function(base, exponent) {
  let result = 1;
  for (let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
};

console.log(power(2, 10));
// → 1024
```

函数也可以有返回值也可以没有返回值（产生副作用，如makeNoise打印字符串），return后面没有表达式，就会导致返回underfined。

函数参数就是正常的变量绑定，只不过初始值是调用者给定。

## 变量和作用域

变量可以在程序的某一部分可见，这部分被叫做变量的作用域。对于定义在任何函数或者块外的变量，作用域是整个程序，你可以在任何时候引用这些变量，这些变量被叫做全局变量（global）。

但是函数参数里的变量只能够在函数内部引用，这些变量被叫做局部变量。每一次对函数的调用，这些变量的实例就会被创建。函数生活在自己的小世界里，不需要知道全局作用域发生了什么。

用let和const声明的变量实际上只在他们声明的块局部可见（块级作用域），所以如果在循环中用这种方式创建变量，循环前后的代码看不见这个变量。在pre-2015的JS，只有函数会创建新的作用域，所以旧的以var方式创建的变量在循环前后都是可见的。如：
```javascript
let x = 10;
if (true) {
  let y = 20;
  var z = 30;
  console.log(x + y + z);
  // → 60
}
// y is not visible here
console.log(x + z);
// → 40
```
每个作用域可以看到外面作用域的变量，有一种例外的情况是，当前局部作用域有一个同名变量，比如下面halve函数内部的引用n是自身作用域的n，而不是外部的n：
```javascript
const halve = function(n) {
  return n / 2;
};

let n = 10;
console.log(halve(100));
// → 50
console.log(n);
// → 10
```

## 嵌套作用域

JS不仅区分全局作用域和局部作用域，块和函数可被嵌套在其它块或者函数中，产生不同程度的局部性。

比如下面的嵌套函数，输出制作鹰嘴豆泥的材料。
```javascript
const hummus = function(factor) {
  const ingredient = function(amount, unit, name) {
    let ingredientAmount = amount * factor;
    if (ingredientAmount > 1) {
      unit += "s";
    }
    console.log(`${ingredientAmount} ${unit} ${name}`);
  };
  ingredient(1, "can", "chickpeas");
  ingredient(0.25, "cup", "tahini");
  ingredient(0.25, "cup", "lemon juice");
  ingredient(1, "clove", "garlic");
  ingredient(2, "tablespoon", "olive oil");
  ingredient(0.5, "teaspoon", "cumin");
};
```
ingredient函数的代码能看见外部的factor变量，但局部变量如unit不能被外部函数看到。

在一个块中可见的变量集合取决于块在程序中的位置。每一个局部作用域能够看见包含他的局部作用域中的变量，所有作用域都可以看见全局作用域。这种变量可见性的策略叫做词法作用域（lexical scoping）。

## 函数作为值

一个函数绑定通常都是以一个特定程序的名字存在的。这样的绑定只定义一次并不会改变，所以很容易混淆函数和他的名字。

但是这俩概念不同。函数值可以做一切其他值可以做的事情，可以用在任何表达式中，作为参数传递给别的函数等等。相似地，一个函数变量也只是一个常规的变量，如果不是constant，也可以被赋新的值。
```javascript
let launchMissiles = function() {
  missileSystem.launch("now");
};
if (safeMode) {
  launchMissiles = function() {/* do nothing */};
}
```
第五章会讨论高阶函数可做的有趣的事情。

## 声明记号

更简单的声明函数的方式如下：
```javascript
function square(x) {
    return x * x;
}
```
这是个函数声明，语句定义了square的绑定并将其指向一个函数，不需要在末尾包含分号且容易书写。

有个细微之处需要注意一下。

```javascript
console.log("The future says:", future());

function future() {
  return "You'll never have flying cars";
}
```

即便函数声明在调用者的下方，也可以正常运行。函数声明并不是常规的自顶向下的控制流。他们在概念上被移动到作用域的顶端并可以被该作用域所有的代码使用。这有时候很有用因为提供了一种机制，不需要担心在使用前还要声明所有的函数。

## 箭头函数
还有第三种函数标记，看起来和其他两种很不一样。用一个箭头而不是function关键字定义函数，也就是`=>`。如：
```javascript
const power = (base, exponent) => {
  let result = 1;
  for (let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
};
```

只有一个参数可以省略表示参数的括号。函数体如果是单个表达式，没有大括号的前提下，会被当成函数返回值返回。
```javascript
const square1 = (x) => {return x * x;}
const square2 = x => x * x;
```
没有参数的箭头函数，参数列表只表示为一个空的括号，如：
```javascript
const horn = () => {
    console.log("hh");
}
```

同时在JS中包含函数表达式和箭头函数并没有什么深层次的原因，除了第六章将要讨论的细微的差异，他们起着同样的作用。箭头函数在2015年被添加，使得更紧凑的书写函数成为一种可能。第五章将会用到很多箭头函数。

## 调用栈

```javascript
function greet(who) {
  console.log("Hello " + who);
}
greet("Harry");
console.log("Bye");
```
函数调用时，控制权交给函数，但是函数需要知道自己结束之后返回的地方，而上下文的保存发生在栈中，当调用函数时，将当前上下文压栈，然后函数返回的时候，将栈顶层的上下文弹出栈，并且用弹出的上下文继续执行指令。

```javascript
function chicken() {
  return egg();
}
function egg() {
  return chicken();
}
console.log(chicken() + " came first.");
// → ??
```
像上述的循环调用没有终止最终就会导致栈溢出。

## 可选参数

下面代码可以正常执行：
```javascript
function square(x) { return x * x; }
console.log(square(4, true, "hedgehog"));
// → 16
```

JS对于函数的传参特别宽容，不像java一样要检查函数签名。如果传递过多参数，简单忽略，传递过少，剩余的参数都将被赋值underfined。

缺点是错误传参时并没有提示。好处是允许一个函数通过不同数量的参数被调用。比如下面的`minus`函数：
```javascript
function minus(a, b) {
  if (b === undefined) return -a;
  else return a - b;
}

console.log(minus(10));
// → -10
console.log(minus(10, 5));
// → 5
```
如果在参数后面写一个等号，后面写一个表达式，那么如果参数没有被调用者给定时，表达式的值将会代替这个参数，所谓的默认参数。比如下面的power函数：
```javascript
function power(base, exponent = 2) {
  let result = 1;
  for (let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
}

console.log(power(4));
// → 16
console.log(power(2, 6));
// → 64
```
power的第二个参数可选，如果没有给定或者传递underfined参数给第二个参数，默认exponent为2，函数的作用表现为计算平方数。

下一章，将会学习一种方式，函数体可以得到传进来的参数列表。这很有用，因为使得函数可以接受任何数量的参数。如`console.log`就做了这样的事情：
```javascript
console.log("c", "0", 2);
```

## 闭包

将函数当作一个值，组合局部变量在每次函数调用的时候创建的特性，带来了一个有趣的问题，当创造局部变量的函数调用不再active的时候，局部变量会怎样呢？

下面的例子展示了这个问题，`wrapValue`创造了一个局部绑定，然后返回了一个函数，这个函数获取并返回了这个局部绑定。

```javascript
function wrapValue(n) {
  let local = n;
  return () => local;
}

let wrap1 = wrapValue(1);
let wrap2 = wrapValue(2);
console.log(wrap1());
// → 1
console.log(wrap2());
// → 2
```

如你所见，两种情况的绑定都正常被获取了，这也很好的验证了，每次函数调用都会创建一个新的局部变量，不同的函数调用不会影响彼此。

这个特性，在封闭的作用域中可以引用本地绑定的一个特定实例，被叫做closure，也就是闭包。一个从局部作用域引用绑定的函数叫做一个闭包（closure）。这种特性不仅使你免于担心绑定的寿命，而且也可以以很创造性的方式使用函数。

我们可以将之前的例子转换成创造一个函数乘以任意数。
```javascript
function multiplier(factor) {
    return number => number * factor;
}
let twice = multiplier(2);
console.log(twice(5));
```

需要一些练习来思考这样的程序，一个好的思维模型是，把函数当作同时包含函数体以及他们被创建的环境。当被调用时，函数体可以看见它被创建时的环境，而不是它被调用的环境。

例子中，multiplier被调用并创造了一个环境，这个环境中factor为2。返回的函数值存储在twice中，记得这个环境。所以当其被调用时，将参数乘以了2。

## 递归

自己调用自己的函数叫做递归函数，只要具备终止条件不会爆栈即可。如power函数的递归版本：
```javascript
function power(base, exponent) {
    if(exponent == 0) {
        return 1;
    } else {
        return base * power(base, exponent - 1);
    }
}
console.log(power(2, 3));
// -> 8
```
这种递归的实现方法更贴近于数学对递归的定义，但是相对于循环版本，速度慢了三倍左右，运行一个简单的循环版本的代价要便宜得多。

速度和优雅需要做个折衷，一边是人类友好，一边是对机器友好。

power函数中，循环版本还算容易理解，没有必要去写一个递归版本。但有些程序为了看上去更加直观，只得放弃一些效率，换取书写的容易。

考虑效率可能让人分神，尤其是复杂的程序设计，再要额外考虑效率可能更让人崩溃。

因此，开始时先保证正确性，毕竟大量的代码并不会执行很多次。非要坚持效率的话，后期在做优化。

递归并不总是一种循环的低效率替代。有时候一些问题更容易用递归实现。经常地，这些问题需要探索或处理很多分支，而这些分支又会衍生出更多分支，比如图算法。

考虑这样的难题：从1开始，要么加5，要么乘3，给定一个数字，你怎么找到这样的序列可以产生这个数字呢？

比如，13可以被到达通过首先乘3，然后加两次5.15不可以被到达。

这是一个递归的实现：
```javascript
function findSolution(target) {
  function find(current, history) {
    if(current == target) {
      return history;
    } else if(current > target) {
      return null;
    } else {
      return find(current + 5, `(${history} + 5)`) || find(current * 3, `(${history} * 3)`);
    }
  }
  return find(1, "1");
}

console.log(findSolution(24));
// -> (((1 * 3) + 5) * 3)
```

这个程序并不会找到最短的操作序列，只是在找到一个满足条件的序列就会停止。

内部的find函数做了递归操作，若当前current等于目标target，则返回history，若当前current大于目标target，则这个分支不存在，返回null，否则小于target，我们通过递归做尝试，可以加5，可以乘以3.如果第一个没有返回null，则返回第一个表达式返回的内容，否则返回第二个表达式返回的内容。不考虑是一个字符串还是null（或运算符的短路效应）。

为了更好理解函数的调用过程，我们拿13举个例子。
```
find(1, "1")
  find(6, "(1 + 5)")
    find(11, "((1 + 5) + 5)")
      find(16, "(((1 + 5) + 5) + 5)")
        too big
      find(33, "(((1 + 5) + 5) * 3)")
        too big
    find(18, "((1 + 5) * 3)")
      too big
  find(3, "(1 * 3)")
    find(8, "((1 * 3) + 5)")
      find(13, "(((1 * 3) + 5) + 5)")
        found!
```
缩进表明了调用栈的深度，第一次find被调用的时候，首先尝试1+5，然后继续向更深层次的递归，直到current大于或者等于target，到最后在这条分支上发现行不通，于是开始尝试乘3的分支，直到遇见一个满足条件的返回值，并且由于逻辑或的特性，就开始一层层向上返回而不再继续后续的计算。

## 生长的函数

有两种自然方式去在你的程序中引入函数：一种是你发现多次写了相似的代码，这样的情况下，更容易引进错误，并且可读性不好。还有一种情况是你需要某种功能，你觉得它值得被包装为一个函数，你将开始于命名这个函数，然后书写它的函数体。你甚至还可能在写这个函数之前已经书写了它的用例函数。

给函数一个好名字的困难性取决于你想包装的这个概念有多清晰明了，举个例子：

我们想要打印农场牛和鸡的数量，零填充动物的数量，后面加上Cows和Chickens。如
```
007 Cows
011 Chickens
```

这需要两个参数，分别是牛的数量还有鸡的数量：
```javascript
function printFarmInventory(cows, chickens) {
  let cowString = String(cows);
  while (cowString.length < 3) {
    cowString = "0" + cowString;
  }
  console.log(`${cowString} Cows`);
  let chickenString = String(chickens);
  while (chickenString.length < 3) {
    chickenString = "0" + chickenString;
  }
  console.log(`${chickenString} Chickens`);
}
printFarmInventory(7, 11);
```

字符串的length属性返回字符串的长度。因此，while循环会在数字字符串加前导0直到他们至少三个字符长。

就当我们马上要交付程序的时候，农民告诉我们还想要打印猪的数量，于是我们重新思考，与其复制粘贴重复的代码，是不是还有更好的方案呢？这是第一种尝试：
```javascript
function printZeroPaddedWithLabel(number, label) {
  let numberString = String(number);
  while (numberString.length < 3) {
    numberString = "0" + numberString;
  }
  console.log(`${numberString} ${label}`);
}

function printFarmInventory(cows, chickens, pigs) {
  printZeroPaddedWithLabel(cows, "Cows");
  printZeroPaddedWithLabel(chickens, "Chickens");
  printZeroPaddedWithLabel(pigs, "Pigs");
}

printFarmInventory(7, 11, 3);
```

这没有问题！但是这个函数名多少有点尴尬。它将打印，零填充以及添加标签三种事情放到一个函数中。

代替把我们程序中大规模的重复部分抽到一个函数中，我们尝试着去用单个concept（概念）：
```javascript
function zeroPad(number, width) {
  let string = String(number);
  while(string.length < width) {
    string = "0" + string;
  }
  return string;
}
function printFarmInventory(cows, chickens, pigs) {
  console.log(`${zeroPad(cows, 3)} Cows`);
  console.log(`${zeroPad(chickens, 3)} Chickens`);
  console.log(`${zeroPad(pigs, 3)} Pigs`);
}
```

拥有一个显而易见的函数名字使得读代码的人很容易的明白它的作用，这个函数不仅在这样的上下文中是有用的，但很多其他场合如打印对齐的数字表格都很有用。

一个书写函数的实用原则是除非你很确定要添加一些小聪明在里面，否则不要添加。可能将你遇到大的每一块功能都写一个通用框架是很吸引人的，要抵制这个强烈的欲望，你将不会完成任何实际的工作，只是会写一堆永远都用不到的代码。

## 函数和副作用

函数可大致分为产生副作用的函数和产生返回值的函数。

第一个printZeroPaddedWithLabel因为其副作用就是打印一行文本被调用，而第二个zeroPad则是因为其返回值被调用。无疑第二种在很多情况下比第一种有用。创造返回值的函数更容易用新的方式去组合相对于直接表现副作用的函数。

一个纯函数（pure function）是一种特定的生成返回值的函数，不仅没有副作用也不依赖于其他代码的副作用。比如，不读取可能会变化的全局变量。一个纯函数值要用相同的参数调用，就会返回相同的值。

没有必要感觉到悲伤去写一个非纯函数，副作用经常也是很有用的，比如console.log。用副作用，一些操作更容易以一种高效的方式去表达，所以计算速度可以是避免纯函数的一个原因。

## 总结

这一章讲述如何写自己的函数。function关键字作为表达式被使用的时候，可以创造一个函数值。被用作语句的时候，可以声明一个绑定，并且给定一个函数作为他的值。箭头函数也可以创造函数。
```javascript
// Define f to hold a function value
const f = function(a) {
  console.log(a + 2);
};

// Declare g to be a function
function g(a, b) {
  return a * b * 3.5;
}

// A less verbose function value
let h = a => a % 3;
```

理解函数一个关键的方面是理解作用域。每一个块创造一个新的作用域。声明在一个块里的参数和绑定是局部可见的。用var声明的绑定表现得不太一样，他们终止于最近的函数作用域或是全局作用域，也就是说，var不受限于块级作用域。

将你的程序要完成的任务分离为不同的函数是很有帮助的。你不会重复太多代码，并且函数也帮助组织代码，从而让不同的部分做特定的事情。

## 练习

### 最小

写一个类似Math.min一样的函数：
```javascript
function min(i1, i2) {
  if(i1 > i2) return i2;
  else return i1;
}
```

### 递归

写一个isEven函数，判断偶性，但是按照递归的方式写，0是偶数，1是奇数，对于其他数字，奇偶性等同自己减2的数字。
```javascript
function isEven(num) {
  if(num < 0) return isEven(-num);
  if(num == 0) return true;
  else if(num == 1) return false;
  else return isEven(num - 2);
}
```
修复num为负的情况，若num为负，返回它的相反数的偶性。

### 豆子计数

```javascript
// Your code here.

console.log(countBs("BBC"));
// → 2
console.log(countChar("kakkerlak", "k"));
// → 4
```
上面的是用例函数
```javascript
function countBs(str) {
  let count = 0;
  for(let i = 0; i < str.length; i++) {
    if(str[i] == "B") count++;
  }
  return count;
}

function countChar(str, character) {
  let count = 0;
  for(let i = 0; i < str.length; i++) {
    if(str[i] == character) count++;
  }
  return count;
}
function countBs(str) {
  return countChar(str, "B");
}
```