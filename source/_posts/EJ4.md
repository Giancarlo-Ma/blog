---
title: EJ4
date: 2018-12-21 14:57:58
categories:
- Eloquent JavaScript
---

# 数据结构：对象和数组

数字，布尔值和字符串类型是数据结构构建的原子。许多类型的数据需要很多的原子类型的数据来组成。对象就允许我们将值组合在一起，还可以包含其他对象来构建复杂的结构。

到目前为止的程序只是操作的简单数据，这一章将会介绍一些基本数据结构。结束的时候，将会知道足够多的知识开始写一些有用的程序。

这一章将会解决现实中编程的例子，引入一些概念。例子代码构建在函数和变量基础上。
<!-- more -->
## 松鼠

下午8点到10点，雅克发现自己变型为毛茸茸的啮齿动物，带着很多毛的尾巴。（松鼠）。

没有变成狼减少了很多麻烦。不是担心吃掉邻居，它担心被邻居家的小猫吃掉。不规律的变形使得雅克怀疑这可能是什么事情触发的。雅克一度认为，可能和它靠近橡树有关，但是避免橡树并不能解决问题。

所以雅克每天记录日志，以及是否触发变形，希望能找到原因。

他要做的第一件事就是用一种数据结构去存储这个信息。

## 数据集

为了用一堆数字，我们首先不得不找到一种在内存中表示的方式。比方说，我们想要表示一个2，3，5，7，11的数字集合。

我们可以用字符串，但是很麻烦，为了获取他们，你还得提取他们，并转换成数字类型。

幸运的是，JS提供了一种存储值序列的数据类型。叫做数组，并且以列表的形式写在中括号中间，被逗号分隔。
```javascript
let listOfNumbers = [2, 3, 5, 7, 11];
console.log(listOfNumbers[2]);
// → 5
console.log(listOfNumbers[0]);
// → 2
console.log(listOfNumbers[2 - 1]);
// → 3
```
获取数组中的元素同样采用中括号标记。

数组下标以0开始，把下标想象成skip的数组元素数量。

## 属性

我们看过一些额类似myString.length和Math.max一样的表达式。这些是获取某个值的属性的表达式。第一种情况，我们获取myString值中的length属性，第二种情况下，我们获取Math对象中的max属性（Math对象是一个数学相关的常量和函数的集合）

差不多所有的JS值都有属性，除了null和underfined。如果尝试在这些空值上获取属性，你将会得到一个错误。
```javascript
null.length;
// -> TypeError: null has no properties
```

两种主要的获取属性的方式是点标记和中括号标记。value.x和value[x]都可以获取value上的x属性，但是不一定是同一个属性。区别在于x是怎么被解释的。当用一个点标记的时候，点后面的单词是属性的字面量的名字。当用一个中括号标记的时候，中括号中间的表达式会被求值（evaluate），为了得到属性的名字。所以value.x获取value名为"x"的属性，而value[x]将x表达式计算求值后转为字符串作为要获取的属性名。

所以如果知道感兴趣的属性叫做color，你可以用value.color。如果想从一个绑定i中提取一个属性名，你可以使用value[i]。属性名是字符串。他们可以是任何字符串，但是点标记只对那些看起来是合法的变量名的属性才有效。如果你想要获取属性名为2或者像John Doe这样的属性名，就必须使用中括号了，也就是value[2]或者value["John Doe"]。

数组中的元素是以数组的属性存储的，并用数字作为属性名。因为你不能用点表示法获取数字，而且通常想用一个变量保存数组的索引，你不得不用中括号去获取数组元素。

数组的length属性告诉我们数组中元素的个数，这个属性名是一个合法的变量名，并且我们预先知道这个属性名。所以为了获取数组长度，我们用array.length，而不是array["length"]，因为前者书写更简单。

## 方法

除了length属性，字符串和数组还包含一些存储着函数值的属性：
```javascript
let doh = "Doh";
console.log(typeof doh.toUpperCase);
// → function
console.log(doh.toUpperCase());
// → DOH
```

每个字符串都有一个toUpperCase属性。被调用的时候，将会返回一个字符串副本，副本里面所有的字母都被转换为大写形式。还有一个toLowerCase，表现为相反的情况。

有趣的是，即使toUpperCase没有传递任何参数，这个函数也用了某种方法获取到了字符串"Doh"，我们调用了该值的属性。这个是如何工作的将在第六章描述。

包含函数的属性通常被叫做他们所属值的方法（methods），toUpperCase是字符串的方法。

这个例子展示了两个可用于修改数组的方法。
```javascript
let sequence = [1, 2, 3];
sequence.push(4);
sequence.push(5);
console.log(sequence);
// → [1, 2, 3, 4, 5]
console.log(sequence.pop());
// → 5
console.log(sequence);
// → [1, 2, 3, 4]
```
push方法在数组末尾加个值，pop移除值并返回被移除的值。

这些有点愚蠢的名字是栈的操作的传统术语。栈是一种数据结构，允许你push值到栈中，pop最后添加的值，后进先出的数据结构。前面的函数调用栈就是这个主意的一个实例。

## 对象

回到松鼠的问题，一组日志条目可以被表示为数组。但是条目不仅仅包含数字和字符串，每一个条目还需要存储一个活动列表和一个布尔值去表明是否产生了变身。理想情况下，我们可以将这些打包在一个值里面并且将这些打包好的值放在一个日志条目数组中。

对象类型的值是任意属性的集合。一种创造对象的方式就是用花括号表达式创建。

```javascript
let day1 = {
  squirrel: false,
  events: ["work", "touched tree", "pizza", "running"]
};
console.log(day1.squirrel);
// → false
console.log(day1.wolf);
// → undefined
day1.wolf = false;
console.log(day1.wolf);
// → false
```
在括号里面，有一个以逗号分隔的属性列表，每个属性有一个名字以及一个冒号，后面跟着属性的值。当一个对象跨越多行的时候，像例子中的缩进使得可读性更好。属性名不是合法的变量名或是数字必须用引号引起来。

```javascript
let descriptions = {
    work: "Went to work",
    "touched tree"L "Touched a tree"
};
```
这意味着括号有两种意义，在语句的开始，他们表明一个块的开始，在其他位置，他们描述一个对象。幸运的是，很少用大括号包裹的对象开始一个语句，所以歧义性不是个问题。

读取一个不存在的属性会返回underfined。

可以用`=`来给对象的属性赋值，如果属性存在就覆盖，否则创建新的键值对。

简要回忆下绑定的触手模型。属性绑定也是相似的。他们grasp值，但是其他的绑定和属性也可能保存着那些相同的值。你可以把对象想象成有任意数量的触角，每一个都有一个名字刺在触角上。

`delete`操作符会从章鱼身上切断一个触角。他是一个一元操作符，当被应用到对象的属性时，会从对象上移除那个名字的属性。这不是很常用，但是可以实现。

```javascript
let anObject = {left: 1, right: 2};
console.log(anObject.left);
// → 1
delete anObject.left;
console.log(anObject.left);
// → undefined
console.log("left" in anObject);
// → false
console.log("right" in anObject);
// → true
```
二元操作符`in`，当给定一个字符串和对象时，将会告诉你是否那个对象有一个特定名字（字符串指定）的属性。将一个对象属性显式设置为underfined和将一个对象的属性删除的区别在于，前者对象仍然拥有那个属性（只是属性并没有一个太有意义的值），然而后者属性不复存在，并且`in`操作符的结果会返回`false`。

为了获取对象属性，可以用`Object.keys`函数，这个函数返回一个以对象键作为值的字符串数组。
```javascript
console.log(Object,keys({x: 0, y: 0, z: 2}));
// -> ["x", "y", "z"]
```
还有一个`Object.assign`函数从一个对象拷贝所有的属性到另一个对象。
```javascript
let objectA = {a: 1, b: 2};
Object.assign(objectA, {b: 3, c: 4});
console.log(objectA);
// -> {a: 1, b: 3, c: 4}
```

数组只不过是一种特定的存储一个序列的东西的对象。如果执行`typeof []`，将会返回"object"。

我们把雅克的日记记作一个对象数组。
```javascript
let journal = [
  {events: ["work", "touched tree", "pizza",
            "running", "television"],
   squirrel: false},
  {events: ["work", "ice cream", "cauliflower",
            "lasagna", "touched tree", "brushed teeth"],
   squirrel: false},
  {events: ["weekend", "cycling", "break", "peanuts",
            "beer"],
   squirrel: true},
  /* and so on... */
];
```

## 可变性

我们很快进入正式的编程了。首先有一点理论知识需要理解。

我们已经知道对象的值是可以改变的。但是早先学习的数字，字符串和布尔值都是不可变的（immutable），也就是不能去改变这些类型的值。

对象以另外的方式去工作。你可以改变对象的属性值，使得同一个对象在不同的时间有着不同的内容。

当我们有两个数字，120和120，我们可以把他们呢看作是相同的数字，不管他们是不是指向同样的物理位。对象的情况有不太一样。有两个引用指向同一个对象和包含相同的属性的不同对象，有着本质的差异。考虑如下代码：
```javascript
let object1 = {value: 10};
let object2 = object1;
let object3 = {value: 10};

console.log(object1 == object2);
// → true
console.log(object1 == object3);
// → false

object1.value = 15;
console.log(object2.value);
// → 15
console.log(object3.value);
// → 10
```
`object1`和`object2`绑定引用的是同一个对象，那也是改变`object1`的属性值也会改变`object2`属性值的原因。`object3`只是恰巧和`object1`初始包含同样的内容，但完全不是一码事。

绑定也是可变的或者是恒定的，但是和他们的值的表现行为无关。即使一个数字类型的值不可变，你可以用一个`let`绑定去追踪一个改变的数字通过改变绑定指向的值。相似地，即便通过一个`const`绑定的指向的对象不会改变，但对象的内容可能会改变。
```javascript
const score = {visitors: 0, home: 0};
// This is okay
score.visitors = 1;
// This isn't allowed
score = {visitors: 1, home: 1};
```
当用JS的`==`操作符比较对象时，比较的是对象的同一性（identity）：只在两个对象精确地是同一个值才返回true。比较不同的对象将会返回false，即便他们有相同的属性。JS没有原生的深度比较操作，也就是根据对象的内容去比较，但是你可以自己写一个，这也是本章最后的练习。

## 日志

```javascript
let journal = [];

function addEntry(events, squirrel) {
  journal.push({events, squirrel});
}
```
这种添加对象的方式有点奇怪，没有写`events: events`这样的形式，只给了属性的名字。其实这只是一种缩写，代表了同样的意思。如果一个在花括号中的属性名后面没有跟着属性值，那么它的值将会从相同名字的变量当中获得。

所以，每天晚上10点或者有时候第二天早上，在爬上顶层书架后，雅克记录下一天：
```javascript
addEntry(["work", "touched tree", "pizza", "running",
          "television"], false);
addEntry(["work", "ice cream", "cauliflower", "lasagna",
          "touched tree", "brushed teeth"], false);
addEntry(["weekend", "cycling", "break", "peanuts",
          "beer"], true);
```
有了这些数据，雅克想用统计学办法去找到什么事件于松鼠化相关联。

相关性（correlation）是一种在统计变量中依赖的量度。变量之间的相关性表示位-1到1之间的数字，0表示不相关，1表示完全正相关，-1表示完全负相关（一个为真，另一个为假)。

为了计算两个布尔值之间的相关性，我们可以用phi coefficient (ϕ)。这是一个公式，输入是观测变量的不同组合的次数频率表，输出是-1到1的数字，表示相关性。

我们可以拿吃披萨这个事情举个例子并且把它放在一个频率表里面，在表中，每一个数字表明那个组合在我们的测量中发生的次数。

计算phi值，发现是0.069，很小的值，吃披萨不足以影响变身。

## 计算相关性

我们可以将一个2*2的表表示为4个元素的数组([76， 9， 4， 1])。我们也可以使用其他的表示，如含有两个数组元素的数组([[76, 9], [4, 1])。或者是一个对象含有"11"和"01"这样的属性名，但是扁平的数组是更加容易的，并且使得获取表元素的表达式更加简洁。我们将会把索引表示为两位二进制数，比如10代表变身为松鼠，但是event没有发生。因为10代表十进制2，所以把数字存储在数组索引为2的位置。

下面是计算phi系数的函数：
```javascript
function phi(table) {
  return (table[3] * table[0] - table[2] * table[1]) /
    Math.sqrt((table[2] + table[3]) *
              (table[0] + table[1]) *
              (table[1] + table[3]) *
              (table[0] + table[2]));
}

console.log(phi([76, 9, 4, 1]));
// → 0.068599434
```
这是计算phi公式的JS表达方式。Math对象的sqrt计算平方根，在标准JS环境中提供。我们必须从我们表中添加两个字段以获取像n1这样的形式。

雅克记录了三个月的日志，结果数据文件存储在JOURNAL绑定中。

为了对每个特定事件提取为2*2的表格，需要循环所有entry，并且计算出事件发生的次数和松鼠化的关系。
```javascript
function tableFor(event, journal) {
  let table = [0, 0, 0, 0];
  for (let i = 0; i < journal.length; i++) {
    let entry = journal[i], index = 0;
    if (entry.events.includes(event)) index += 1;
    if (entry.squirrel) index += 2;
    table[index] += 1;
  }
  return table;
}

console.log(tableFor("pizza", JOURNAL));
// → [76, 9, 4, 1]
```
因为表示索引的二进制数字的首位代表是否松鼠化，第二位代表相应事件是否发生，所以第一位权重为2，第二位权重为1。首先初始化了要返回的频率表。我们遍历每个entry，如果事件发生了，权重加1，而如果松鼠化了，权重加2.最终，将对应权重的索引相应赋值即可。循环结束后，返回table。

数组有一个includes方法用来检查是否给定值在数组中，类似in操作符在对象中的地位。

`tableFor`返回了对于给定事件的频率表。

剩下的就是对于每种记录的事件去找到是否有某件事件特别突出。

## 数组循环

在`tableFor`函数中，有一个像这样的循环：
```javascript
for (let i = 0; i < JOURNAL.length; i++) {
  let entry = JOURNAL[i];
  // Do something with entry
}
```
这种类型的循环在JS中很常见，遍历数组每次取出一个元素做操作。

有一种更简洁的方式去书写这种形式的循环：
```javascript
for(let entry of JOURNAL) {
  console.log(`${entry.events.length} events.`);
}
```
这种形式的for循环，将会遍历of后面的元素值。不仅数组可以用，字符串也可以用还有一些其他的数据结构也可以的。我们将会在第六章讨论他的工作机制。

## 最终分析

我们需要计算数据集中每种事件的相关性。所以我们首先需要找到每种类型的事件：
```javascript
function journalEvents(journal) {
  let events = [];
  for (let entry of journal) {
    for (let event of entry.events) {
      if (!events.includes(event)) {
        events.push(event);
      }
    }
  }
  return events;
}

console.log(journalEvents(JOURNAL));
// → ["carrot", "exercise", "weekend", "bread", …]
```
这个函数就是对journal进行遍历，然后entry里面的events数组再进行遍历，观察是否已经遇到过该事件，如果没遇到，就简单push到数组中。

通过遍历所有事件并把它们添加到events数组中，函数收集了每种类型的事件。
```javascript
for (let event of journalEvents(JOURNAL)) {
  console.log(event + ":", phi(tableFor(event, JOURNAL)));
}
// → carrot:   0.0140970969
// → exercise: 0.0685994341
// → weekend:  0.1371988681
// → bread:   -0.0757554019
// → pudding: -0.0648203724
// and so on...
```
如此一来我们可以看到所有事件的相关性了。

大多数的相关性接近于0。我们来筛选结果只展示那些大于0.1或者小于-0.1的结果。
```javascript
for (let event of journalEvents(JOURNAL)) {
  let correlation = phi(tableFor(event, JOURNAL));
  if (correlation > 0.1 || correlation < -0.1) {
    console.log(event + ":", correlation);
  }
}
// → weekend:        0.1371988681
// → brushed teeth: -0.3805211953
// → candy:          0.1296407447
// → work:          -0.1371988681
// → spaghetti:      0.2425356250
// → reading:        0.1106828054
// → peanuts:        0.5902679812
```
有两个特别显著的影响因素，吃花生表现为强烈的正相关，而刷牙表现为显著负相关。

当雅克吃花生就会变身，而刷牙就会回到原型。如果他不是一个再牙齿卫生方面特别懒散的人，他将不会注意
到它的痛苦。

知道了这个，雅克停止吃花生，并且再也没有继续变身。

## 深度的数组论

完成这一章之前，介绍更多的对象相关的概念。让我们从一些有用的数组方法开始。

我们学习了push和pop，在数组末尾添加或者移除元素。对应的在数组开头添加或者删除元素的方法是unshift和shift。
```javascript
let todoList = [];
function remember(task) {
  todoList.push(task);
}
function getTask() {
  return todoList.shift();
}
function rememberUrgently(task) {
  todoList.unshift(task);
}
```
程序管理了一个任务队列，通过调用remember("groceries")，当准备做某件事情的时候，调用getTask()去获得并从队列移除头部的元素。rememberUrgently方法也添加一个任务，但是是将任务添加到队首而不是队列尾部。

为了寻找一个特定的值的位置，数组提供了indexOf方法，如果找到返回位置的索引，找不到返回-1。为了从末尾开始搜索，有一类似的方法叫做lastIndexOf。

```javascript
console.log([1, 2, 3, 2, 1].indexOf(2));
// → 1
console.log([1, 2, 3, 2, 1].lastIndexOf(2));
// → 3
```

indexOf和lastIndexOf都有一个可选大的第二个参数，表明从哪里开始搜索。

另外一个基本的数组方法是slice，接受起始和终止索引，返回一个数组只包含他们之间的元素。包含start处的元素，但不包含end处元素。
```javascript
console.log([0, 1, 2, 3, 4].slice(2, 4));
// → [2, 3]
console.log([0, 1, 2, 3, 4].slice(2));
// → [2, 3, 4]
```
当end index没有给定，那么一直到数组末尾。如果起始元素没给定，返回原数组的copy。

concat拼接数组，类似字符串的+操作符。下面是个例子：
```javascript
function remove(array, index) {
  return array.slice(0, index)
    .concat(array.slice(index + 1));
}
console.log(remove(["a", "b", "c", "d", "e"], 2));
// → ["a", "b", "d", "e"]
```
从原数组移除给定位置的元素。

如果传递给concat的参数不是一个数组，那么那个值会简单被添加到数组中，就好像它原来是一个单值数组一样。

## 字符串和他们的属性

可以从字符串中读取length和toUpperCase这样的属性，但是想添加一个新属性，是不可以的。
```javascript
let kim = "Kim";
kim.age = 88;
console.log(kim.age);
// → undefined
```
字符串，数字和布尔值不是对象，即便你给他们添加一些新的属性，他们也不会抱怨，而只是简单的忽略。像早先提及的那样，这样的值是immutable的并且不可以被改变。

但是这些类型确实有内建的属性。如字符串的slice和indexOf方法，类似于数组的同名方法。
```javascript
console.log("coconuts".slice(4, 7));
// → nut
console.log("coconut".indexOf("u"));
// → 5
```

一个区别是，字符串的indexOf方法可以搜索一个包含多个字符的字符串，数组方法只搜索单个元素。

```javascript
console.log("one two three".indexOf("ee"));
// → 11
```
字符串的trim方法从字符串的起始和结尾移除空白（space，newlines，tabs和相似的字符）。
```javascript
console.log("  okay \n ".trim());
// → okay
```

zeroPad函数也作为一个方法存在着。它叫做padStart并且接收想要的填充长度和填充字符作为参数。
```javascript
console.log(String(6).padStart(3, "0"));
// → 006
```

可用split方法，以给定字符串分隔字符串，并用join方法将其再拼接起来。
```javascript
let sentence = "Secretarybirds specialize in stomping";
let words = sentence.split(" ");
console.log(words);
// → ["Secretarybirds", "specialize", "in", "stomping"]
console.log(words.join(". "));
// → Secretarybirds. specialize. in. stomping
```
字符串可以被重复，用repeat方法，创建一个新的字符串包含原字符串的几份拷贝拼接在一起。

```javascript
console.log("LA".repeat(3));
// -> LALALA
```

我们已经学习了字符串的length属性，获取字符串中的字符就像获取数组元素一样（有一个警告会在第五章讨论）。
```javascript
let string = "abc";
console.log(string.length);
// → 3
console.log(string[1]);
// → b
```
## REST参数

对于一个函数来说，接收任意数量的参数是很有用的。比如，Math.max就返回接受的任意参数中最大的那个。

为了实现这个，需要在最后一个参数的前面放三个点号。如
```javascript
function max(...numbers) {
  let result = -Infinity;
  for (let number of numbers) {
    if (number > result) result = number;
  }
  return result;
}
console.log(max(4, 1, 9, -2));
// → 9
```

当这样的函数被调用的时候，rest parameter（rest参数）就被绑定到一个包含所有参数的数组上了。如果前面还有其它参数，他们的值不会是数组的一部分。在max函数中，rest参数前不包含任何参数，所以它将会保存所有参数。

可以用相似的三点标记来调用一个函数，并提供一个参数数组。
```javascript
let numbers = [5, 1, 7];
console.log(max(...numbers));
// → 7
```
这将会将数组伸展开来，将数组元素作为独立参数传递给函数。可以混合这种传参方式和其他参数，如`max(9, ...numbers, 2)`。

中括号数组标记也允许三点操作符去展开另一个数组到新数组中：
```javascript
let words = ["never", "fully"];
console.log(["will", ...words, "understand"]);
// → ["will", "never", "fully", "understand"]
```

## Math对象

Math就是一个和数学相关工具函数的百宝箱。如Math.max，Math.min和Math.sqrt。

Math对象作为一个容器将一堆相关的函数关联起来。有且仅有一个Math对象，作为一个值的话他是没有任何用途的。同时，还提供了命名空间，所以这些函数和值不需要是全局变量。

有太多的全局绑定会污染命名空间。越多的名字被使用的话，就可能会再将来意外覆盖存在的绑定。比如，可能你在程序中命名了max变量，因为JS内建的max方法在Math对象中，所以我们不担心会重写它。

许多语言会避免或者至少是警告你，如果定义了一个已经使用过的变量名。JS的let和const是这样做的，但是var和function以及标准绑定（standard binding）却没有这种机制。

回到Math对象，关于三角函数，Math对象有cos，sin，tan，asin，acos，atan方法供使用。此外，还有pi的近似Math.PI。关于常量，编程习惯通常是将常量全部写为大写字母。

```javascript
function randomPointOnCircle(radius) {
  let angle = Math.random() * 2 * Math.PI;
  return {x: radius * Math.cos(angle),
          y: radius * Math.sin(angle)};
}
console.log(randomPointOnCircle(2));
// → {x: 0.3667, y: 1.966}
```
Math.random返回0到1之间的伪随机数（左闭右开）。
```javascript
console.log(Math.random());
// → 0.36993729369714856
console.log(Math.random());
// → 0.727367032552138
console.log(Math.random());
// → 0.40180766698904335
```

虽然计算机是确定性的机器，也就是说，输入相同，输出也相同，但是他们也可能产生一些看上去随机的数字。为了实现这个，计算机会保存一些隐藏值，每当你想要一个随机数，就会根据这个隐藏值执行一系列复杂计算得到一个数字。用这种方式，可以产生全新的难以预测的看上去随机的数字。

如果要产生随机整数，需要结合Math.floor方法，该方法向下取整。
```javascript
console.log(Math.floor(Math.random() * 10));
// → 2
```
该表达式等可能的产生0到9的随机数。

此外还有Math.ceil（向上取整）,Math.round（四舍五入），Math.abs（取绝对值）。

## 解构（destructuring）

回忆下phi函数：
```javascript
function phi(table) {
  return (table[3] * table[0] - table[2] * table[1]) /
    Math.sqrt((table[2] + table[3]) *
              (table[0] + table[1]) *
              (table[1] + table[3]) *
              (table[0] + table[2]));
}
```

这种写法有点尴尬。参数是个数组绑定，但是我们更想要数组元素的绑定，如`let n00 = table[0]`等等。幸运的是，有一种在JS中很简洁的方式去实现。

```javascript
function phi([n00, n01, n10, n11]) {
  return (n11 * n00 - n10 * n01) /
    Math.sqrt((n10 + n11) * (n00 + n01) *
              (n01 + n11) * (n00 + n10));
}
```

对于let，var和const声明的绑定都是可以的。如果你知道要绑定的值是数组，就可用方括号去窥探数组里面的值，绑定它的内容。

一个相似的窍门是对于对象，用花括号而不是方括号。
```javascript
let {name} = {name: "Faraji", age: 23};
console.log(name);
// → Faraji
```

注意如果尝试解构null或者underfined，会得到一个错误，有点像直接从null或者underfined上面获取属性。

## JSON

因为属性只是grasp它的值，而不是包含他。对象和数组只是以保存地址的位串存储的，也就是他们存放内容的地址。所以一个二维数组对于内层的数组至少包含一块内存区域，外层数组还有一块内存区域，包含着二进制数代表内部数组的位置。

如果你想要将数据保存到文件并用互联网发送它。你不得不将这些内存地址转换为一个可以发送或者存储的描述。

我们能做的就是serialize(序列化)这个数据。意味着将他转换为平的描述（flat description）。一个受欢迎的序列化格式是JSON，代表着JavaScript Object Notation。在web上广泛用于数据存储和通讯格式，甚至是除了JS之外的其他语言。

JSON看上去和JS书写数组和对象的方式很相似，但是有一些限制。所有属性名必须由双引号包裹，并且只能是简单的数据表达式，不允许函数调用，变量或者任何其他涉及到计算的东西。注释不允许在JSON中。

在JSON中，一个日志条目看起来可能像是这样：
```json
{
  "squirrel": false,
  "events": ["work", "touched tree", "pizza", "running"]
}
```
JS提供了JSON.stringify和JSON.parse方法。第一种方法接收JS值，返回JSON编码的字符串。第二个接受一个JSON字符串，转换为它译码的JS表示。
```javascript
let string = JSON.stringify({squirrel: false,
                             events: ["weekend"]});
console.log(string);
// → {"squirrel":false,"events":["weekend"]}
console.log(JSON.parse(string).events);
// → ["weekend"]
```

## 总结
对象和数组（是一种特殊对象）提供了一种将多个值整合到一个值当中的方法。

大多对象都有属性，除了null和underfined。属性可通过点符号或者中括号标记获取。

可以用特殊形式的for循环遍历数组`for(let element of array)`。

## 练习

### 范围和

我们曾见过这样的形式：`console.log(sum(range(1, 10)))`

写一个`range`函数接收两个参数，start和end，并返回一个数组，包含start元素和end元素的所有数字。

```javascript
function range(start, end) {
  res = [];
  for(let i = start; i <= end; i++) {
    res.push(i);
  }
  return res;
}
```

然后写一个sum函数。
```javascript
function sum(arr) {
  let res = 0;
  for(let i of arr) {
    res += i;
  }
  return res;
}
```
让range函数接收一个可选的第三个参数，表示步长。
```javascript
function range(start, end, step = 1) {
  arr = []
  if(step > 0) {
    for(let i = start; i <= end; i += step) {
      arr.push(i);
    }
  } else if(step < 0) {
    for(let i = start; i >= end; i += step) {
      arr.push(i);
    }
  }
  return arr;
}
```

### 反转一个数组

分别写函数`reverseArray`，产生一个全新的数组，包含原数组的反转元素；函数`reverseArrayInPlace`，原地反转原数组。

```javascript
function reverseArray(arr) {
  newarr = [];
  for(let i = arr.length - 1; i >= 0; i--) {
    newarr.push(arr[i]);
  }
  return newarr;
}
```

```javascript
function reverseArrayInPlace(arr) {
  function swap(arr, i, j) {
    let temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
  }

  for(let i = 0; i < arr.length / 2; i++) {
    swap(arr, i, arr.length - i - 1);
  }
}
```

### 链表

```javascript
let list = {
  value: 1,
  rest: {
    value: 2,
    rest: {
      value: 3,
      rest: null
    }
  }
};
```

写一个`arrayToList`方法，给定`[1,2,3]`这样的数组，返回上面的list结构。
```javascript
function arrayToList(arr) {
  let l = arr.length;
  let obj = {};
  let p = obj;
  for(let i = 0; i < l; i++) {
    p.value = arr[i];
    if(i == l - 1) {p.rest = null; break;}
    p.rest = {};
    p = p. rest;
  }
  return obj;
}
```

写一个`listToArray`方法，给定上述list格式，返回数组表示。
```javascript
function listToArray(lst) {
  let obj = lst;
  let arr = [];
  while(obj != null) {
    arr.push(obj.value);
    obj = obj.rest;
  }
  return arr;
}
```
写一个`prepend`方法在list前部添加一个元素，返回一个新的list。
```javascript
function prepend(item, lst) {
  let newlst = {};
  newlst.value = item;
  newlst.rest = lst;
  return newlst;
}
```

写一个返回list中第n个位置上的value。

```javascript
function nth(lst, index) {
  let i = 0;
  let p = lst;
  while(p != null && i++ != index) 
    p = p.rest;
  if(p == null) return;
  return p.value;
}
```

写一个递归版本的nth函数
```javascript
function nth(lst, index) {
  if(lst == null) return;
  if(index === 0) return lst.value;
  else return nth(lst.rest, index - 1);
}
```

### 深度比较

`==`比较对象基于同一性，即两个变量指向同一个实际的对象。但是有时候想要根据实际的属性比较对象的值。写一个深度比较函数基于对象的属性进行比较。
```javascript
function deepEqual(a, b) {
  if(a == b) return true;
  
  if(a == null || typeof a != 'object' || b == null || typeof b != 'object') return false;

  let arrA = Object.keys(a), arrB = Object.keys(b);

  if(arrA.length != arrB.length) return false;

  for(let key of arrA) {
    if(!arrB.includes(key) || !deepEqual(a[key], b[key])) return false;
  }
  return true;
} 
```