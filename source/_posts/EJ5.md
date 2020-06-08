---
title: EJ5
date: 2018-12-25 17:12:18
categories:
- Eloquent JavaScript
---

# 高阶函数

我们首先来看两个范围和程序的例子：
```javascript
let total = 0, count = 1;
while (count <= 10) {
  total += count;
  count += 1;
}
console.log(total);
```

```javascript
console.log(sum(range(1, 10)));
```
显而易见，第二种策略更加清晰且不易出错。因为我们将程序设计在一个词汇表框架中，对一个范围内的数字求和无关循环和计数器，而是关于范围(range)以及和(sum)函数。

虽然sum和range内部也会包含循环，计数器等，但他们比将整个程序写在一起，表达了更简单的概念。

<!-- more -->

## 抽象

在编程中，这种类型的词汇表叫做abstraction(抽象)。抽象隐藏程序实现的细节，赋予我们一种在更高层次考虑问题的能力。

## 抽象重复

普通函数也是一种构建抽象的好方法，但是有时远远不够。

重复做一件事对于一个程序来说是很常见的。可以写个for循环，比如：
```javascript
for(let i = 0; i < 10; i++) {
    console.log(i);
}
```
可以做如下改进：
```javascript
function repeatLog(n) {
  for (let i = 0; i < n; i++) {
    console.log(i);
  }
}
```
但我们要想有除打印以外的行为时，这种方法就不奏效了。于是我们想把行为抽象成函数，由于JS中函数也是值，所以是可行的。
```javascript
function repeat(n, action) {
  for(let i = 0; i < n; i++) 
    action(i);
}

let labels = [];
repeat(5, i => {
  labels.push(`Unit ${i + 1}`);
})
console.log(labels);
// -> ["Unit 1", "Unit 2", "Unit 3", "Unit 4", "Unit 5"]
```

## 高阶函数

接收函数作为参数，或者返回函数的函数叫做高阶函数（high order function）。高阶函数不仅允许我们抽象值，更实现了对行为的抽象。如，我们可以有创造新函数的函数。
```javascript
function greaterThan(n) {
  return m => m > n;
}

let greaterThan10 = greaterThan(10);
console.log(greaterThan10(11));
// -> true
```

我们也可以实现改变别的函数的函数：
```javascript
function noisy(f) {
  return (...args) => {
    console.log("calling with", args);
    let result = f(...args);
    console.log("called with", args, ", returned", result);
    return result;
  };
}
noisy(Math.min)(3, 2, 1);
// → calling with [3, 2, 1]
// → called with [3, 2, 1] , returned 1
```

我们甚至可以提供新的控制流：
```javascript
function unless(test, then) {
  if (!test) then();
}

repeat(3, n => {
  unless(n % 2 == 1, () => {
    console.log(n, "is even");
  });
});
// → 0 is even
// → 2 is even
```

数组有个`forEach`方法，有点像是高阶函数的`for/of`版本。
```javascript
["A", "B"].forEach(l => console.log(l));
// -> A
// -> B
```

## script数据集

高阶函数擅长用于数据处理领域。我们本章使用关于script的数据集。

回忆第一章的Unicode码，Unicode为每种语言中的字符都分配一个数字。它们中的大多数都关联到一个特定的script。

数据集包含140种Unicode中定义的script。绑定`SCRIPTS`包含一份对象数组，每一个对象描述了一个script。

```javascript
{
  name: "Coptic",
  ranges: [[994, 1008], [11392, 11508], [11513, 11520]],
  direction: "ltr",
  year: -200,
  living: false,
  link: "https://en.wikipedia.org/wiki/Coptic_alphabet"
}
```

每个对象包含Unicode为其分配的数字范围，起源时间，是否仍在使用中，和一个更多信息的链接。方向可以是"ltr"(left to right),"rtl","ttb"(top to bottom)。ranges是左闭右开区间。

## 过滤数组

为了找到还在使用的script，下面的函数是有用的。
```javascript
function filter(array, test) {
  let passed = [];
  for(let element of array) {
    if(test(element)) {
      passed.push(element);
    }
  }
  return passed;
}
console.log(filter(SCRIPTS, script => script.living))
```

filter是一个pure函数，不修改给定的数组。返回过滤过后的新数组。

像`forEach`一样，`filter`是一个标准的数组方法。例子只是展示了它的内部实现。

```javascript
console.log(SCRIPTS.filter(s => s.direction == "ttb"));
// → [{name: "Mongolian", …}, …]
```

## 用map转换

我们现在有一个对象数组，但我们想要一个名字数组。

map方法通过将函数应用到每个数组元素上，并且返回一个新构建的数组。数组长度不变，但内容已从函数返回值做了相应的映射。

```javascript
function map(array, transform) {
  let mapped = [];
  for(let element of array) {
    mapped.push(transform(element));
  }
  return mapped;
}

let rtlScripts = SCRIPTS.filter(s => s.direction == 'rtl');
console.log(map(rtlScripts, s => s.name));
// → ["Adlam", "Arabic", "Imperial Aramaic", …]
```
像`forEach`和`filter`一样，`map`也是标准的数组方法。

## 用reduce汇总

另一种关于数组的常规的操作时从数组元素上计算单个值。我们反复出现的例子，对一些数字求和，就是这样的实例。另一个例子是找到拥有最多字符的书写体。

代表这种模式的操作是`reduce`，有时也被叫做`fold`。通过处理当前数组中的值并把它和current value组合，当处理数字和时，开始于0，对于每个元素，将其加到和中。

reduce接收三个参数，除了数组，还有一个组合函数，以及一个初始值。
```javascript
function reduce(array, combine, start) {
  let current = start;
  for(let element of array) {
    current = combine(current, element);
  }
  return current;
}

console.log(reduce([1, 2, 3, 4], (a, b) => a + b, 0));
// -> 10
```
标准的JS数组reduce方法，还有一个便利的地方。如果给定数组包含至少一个元素，允许不给定start元素，默认会将第一个元素当作start，并从第二个元素开始reduce。

```javascript
console.log([1, 2, 3, 4].reduce((a, b) => a + b));
// -> 10
```
为了使用reduce方法找到含有最多字符的script，我们需要这样编写代码。
```javascript
function characterCount(script) {
  return script.ranges.reduce((count, [from, to] => count + (to - from), 0);
}
console.log(SCRIPTS.reduce((a, b) => {
  return characterCount(a) < characterCount(b) ? b : a;
}));
// -> {name: "Han", ...}
```

## 可组合性

考虑下不用高阶函数我们怎么书写前面的程序。
```javascript
let biggest = null;
for(let script of SCRIPTS) {
  if(biggest == null || characterCount(biggest) < characterCount(script)) {
    biggest = script;
  }
}
console.log(biggest);
// -> {name: "Han", ...}
```
这依然是可读的。

当你需要compose操作的时候，高阶函数就会显示出它的光芒。作为一个例子，让我们对于活和死的书写体，找到他们的平均起源年龄。
```javascript
function average(array) {
  return array.reduce((a, b) => a + b) / array.length;
}
console.log(Math.round(average(SCRIPTS.filter(x => x.living).map(x => x.year)));
// -> 1188
console.log(Math.round(average(SCRIPTS.filter(x => !x.living).map(x => x.year))));
// -> 188
```
将上述过程看作管线化操作，首先过滤出living的script，然后从对象中取出year，然后做平均，然后做round。

也可以写一个大循环。
```javascript
let total = 0, count = 0;
for(let script of SCRIPTS) {
  if(script.living) {
    total += script.year;
    count += 1;
  }
}
console.log(Math.round(total / count));
```
但是可读性不好，而且也不容易抽象出average函数。

就计算机实际所做的事情而言，这两种方法不大相同。第一种方法通过filter和map构建数组。第二种方法只计算一些数字，做更少的事情。通常可以支付的起易读程序的代码，但是处理大数组的时候，第二种方法在速度上的提升是值得我们考虑的。

## 字符串和字符代码

数据集的一种用途是找到文本使用的script。

每个script包含一个range数组，所以给定一个字符代码，我们可以使用一个函数找到对应的script（如果存在的话）。

```javascript
function characterScript(code) {
  for(let script of SCRIPTS) {
    if(script.ranges.some(([from, to]) => { return code >= from && code < to;
    })) {
      return script;
    }
  }
  return null;
}
console.log(characterScript(121));
// -> {name: "Latin", ...}
```

some是另外一个高阶函数，只要数组中一个元素符合条件，就返回true。

但我们怎么获得字符串中的character code呢？

第一章中提到JS字符串编码为16bit数字序列。这些被叫做code units。我们可以很容易的书写程序假装code units和字符是同一个东西。如果你的语言不使用two-unit字符，那是没问题的。但是如果使用汉字字符，这样的程序就会出现问题。幸运的是，随着emoji的出现，所有人开始使用two-unit字符，处理这样的问题就被均摊了。

不幸的是，JS中字符串的操作，如获取字符串的length属性以及通过中括号获取特定位置字符，只处理code units。
```javascript
// Two emoji characters, horse and shoe
let horseShoe = "🐴👟";
console.log(horseShoe.length);
// → 4
console.log(horseShoe[0]);
// → (Invalid half-character)
console.log(horseShoe.charCodeAt(0));
// → 55357 (Code of the half-character)
console.log(horseShoe.codePointAt(0));
// → 128052 (Actual code for horse emoji)
```
JS的`charCodeAt`方法给一个code unit而不是一个完整的character code。`codePointAt`方法，给定完整的Unicode字符。所以我们使用`codePointAt`方法获取字符串中的字符。但是`codePointAt`方法的参数仍然是一个code units的索引。所以为了遍历到所有的字符串中的字符，我们仍然要解决判断字符占据一个还是两个code units的问题。

在之前的章节，我们提到`for/of`循环也能被用于字符串。像`codePointAt`一样，这种类型的循环是在人们意识到UTF16所带来的问题时诞生的。当使用这种方法遍历字符串时，得到的是真实的字符，而不是code units。
```javascript
let roseDragon = "🌹🐉";
for (let char of roseDragon) {
  console.log(char);
}
// → 🌹
// → 🐉
```
如果你有一个字符（可能是一个或两个code units），可以用`codePointAt`方法获取它的code。

## 识别文本

我们有一个`characterScript`函数以及正确遍历字符串的方法。下一步就是计算属于每个script的字符数量。下面的counting抽象将会有用。
```javascript
function countBy(items, groupName) {
  let counts = [];
  for(let item of items) {
    let name = groupName(item);
    let known = counts.findIndex(c => c.name == name);
    if(known == -1) {
      counts.push({name, count: 1});
    } else {
      counts[known].count++;
    }
  }
  return counts;
} 

console.log(countBy([1, 2, 3, 4, 5], n => n > 2));
// → [{name: false, count: 2}, {name: true, count: 3}]
```
`countBy`函数接受一个collection参数（可用for/of遍历），以及一个函数用于计算给定元素的group name。返回一个对象数组，每一个数组元素对应一组以及该组的元素数量。

其中用到了`findIndex`数组方法，这个方法有点类似于`indexOf`，不同的是，该方法返回第一次让参数中的方法返回true的数组元素，如果没有满足条件的元素，返回-1。

使用`countBy`方法，我们可以写一个函数告诉我们一段文本中使用的script。

```javascript
function textScripts(text) {
  // 获得数据集中可以识别的script
  let scripts = countBy(text, char => {
    let script = characterScript(char.codePointAt(0));
    return script ? script.name : "none";
  }).filter(({name}) => name != "none");

  // 计算script总数，避免文本中全部都是不在数据集中的script
  let total = scripts.reduce((n, {count}) => n + count, 0);
  if(total == 0) return "No scripts found";
  // 映射为字符串
  return scripts.map(({name, count}) => {
    return `${Math.round(count * 100 / total)}% ${name}`
  }).join(", "); 
}
console.log(textScripts('英国的狗说"woof", 俄罗斯的狗说"тяв"'));
// → 61% Han, 22% Latin, 17% Cyrillic
```
函数收i按根据字符的名字计数，这一步使用了`characterScript`函数，并在如果字符不是任何script时用字符串"none"做了回退处理。filter可以过滤掉结果数组中name为"none"的数组元素，因为我们不想考虑那些元素。

为了计算百分比，我们需要计算字符的总数。如果没有有效字符被找到，那么返回一个特定的字符串。否则，用map方法形成一个个人类易读的字符串，然后用join方法拼接起来。

## 总结

JS中可以传递函数值给其他的函数是特别有用的。可以提供一个缺口，而调用这些传进来的函数获得的值，可以填上这个缺口。

数组提供了一系列的高阶方法。可以用`forEach`方法遍历数组中的元素。`filter`方法过滤元素，只留下函数返回值为true的元素。将数组中的元素通过一个函数转换用`map`方法。可以用`reduce`方法将数组中的所有元素组合成为一个值。`some`方法测试是否数组中有任何一个元素满足测试函数的条件。`findIndex`找到第一个匹配预测函数特征的元素位置。

## 练习

### flattening

用`reduce`方法组合`concat`方法flatten一个二维数组，使其拥有原数组的所有元素。
```javascript
let arrays = [[1, 2, 3], [4, 5], [6]];

console.log(arrays.reduce((arr, cur) => arr.concat(cur)));
// → [1, 2, 3, 4, 5, 6]
```

### own loop

```javascript
function loop(initial, test, update, body) {
  for(let i = initial; test(i); i = update(i)) {
    body(i);
  }
}

loop(3, n => n > 0, n => n - 1, console.log);
// -> 3
// -> 2
// -> 1
```

### everything

类似于`some`方法，数组也有一个`every`方法。在数组中所有元素都满足预测函数的时候返回`true`。换一种说法，`some`有点类似于`||`操作符作用到数组上。而`every`类似于`&&`操作符。

`every`方法接收两个参数，一个数组和一个预测函数作为参数。写两个版本的`every`方法。一个用循环，一个用`some`方法。

```javascript
function every(array, test) {
  for(let element of array) {
    if(!test(element)) return false;
  }
  return true;
}
```
```javascript
function every(array, test) {
  !array.some(element => !test(element))
}
```
### dominant writing direction

计算一串文本中主要的文字书写方向，由script对象的direction属性给出，可以是"ltr""rtl""ttb"。

`characterScript`和`countBy`方法可能是有用的。

```javascript
function dominantDirection(text) {
  // Your code here.
  let counts = countBy(text, char => {
    let script = characterScript(char.codePointAt(0));
    return script ? script.direction : "none";
  }).filter(({name}) => name != "none");

  if(counts.length == 0) return 'ltr';

  return counts.reduce((a, b) => a.count > b.count ? a : b).name;
}

console.log(dominantDirection("Hello!"));
// → ltr
console.log(dominantDirection("Hey, مساء الخير"));
// → rtl
```