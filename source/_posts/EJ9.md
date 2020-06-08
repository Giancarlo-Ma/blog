---
title: EJ9
date: 2019-01-05 23:03:36
categories:
- Eloquent JavaScript
---

# 正则表达式

编程工具和技术以一种混乱的，进化的方式生存和传播。并不总是漂亮的或聪明的那些最后会胜出，而是那些在市场中功能良好或者刚好与另一项成功的技术结合在一起的那些。

这一章，我将会讨论一个这样的工具，正则表达式。正则表达式是一种在字符串数据中描述模式的方式。它们是一个小的独立的语言，并且是JS和许多语言和系统的一部分。

正则表达式是难于对付的，同时又是极度有用的。他们的语法看上去比较神秘，并且JS为它们提供的编程接口难于处理。但是它们是检查和处理字符串很有力的工具。正确理解正则表达式将使你成为一个更高效的程序员。

<!-- more -->

## 创造一个正则表达式

一个正则表达式就是一种类型的对象。它既可以通过`RegExp`构造器创建也可以以`/`字符间包含模式这样的字面量形式来创建。

```javascript
let re1 = new RegExp("abc");
let re2 = /abc/;
```

这两个正则表达式都代表了相同的模式：a后面跟着b后面跟着c。

当使用`RegExp`构造器时，模式以字符串的形式书写，所以通常用于反斜线的规则在这里也适用。

第二种模式出现在正斜线之间，对待反斜线有些许不同。首先，因为一个正斜线终止模式，我们需要在任何组成模式的正斜线前加一个反斜线。**除此之外，非特殊字符组成部分（如\n）的反斜线将会被保留，而不是像字符串中被忽略，**并且改变模式的意义。一些字符，像问号和加号，在正则表达式中有特殊的含义，如果想表达字符本身必须前置反斜线。
```javascript
let eighteenPlus = /eighteen\+/;
```

## 测试匹配

正则表达式对象有许多方法。最简单的一个就是`test`方法。如果给他传递一个字符串，将会返回是否字符串包含表达式中的模式。

```javascript
console.log(/abc/.test("abcde"));
// → true
console.log(/abc/.test("abxde"));
// → false
```

仅由非特殊字符组成的正则表达式仅仅表示这个字符序列。如果*abc*出现在我们要测试的字符串的任何位置*不仅仅是开始），`test`都将返回`true`。

## 字符集

判断一个字符串是否包含`abc`也可以通过调用`indexOf`实现。正则表达式允许我们表达更复杂的模式。

假设我们想要匹配任意数字。在正则表达式中，在方括号中放置一组字符，那么会匹配方括号中的任意字符。

下面的表达式都会匹配所有的包含数字的字符串。
```javascript
console.log(/[0123456789]/.test("in 1992"));
// → true
console.log(/[0-9]/.test("in 1992"));
// → true
```
在方括号内部，两个字符间的短横线(hyphen)可以被用于表明一个范围间的字符，其中的顺序由字符的*Unicode*编码决定。在这个顺序中，字符0到9彼此相邻（代码48到57），所以`[0-9]`覆盖所有字符并且匹配任意数字。

一些常见的字符组都有自己内置的缩写。数字是他们其中一个：`\d`和`[0-9]`有相同的含义。

`\d` 任意数字字符

`\w` 一个字母数字字符（单词字符）

`\s` 任意空白字符（空格，tab，newline和类似的字符）

`\D` 非数字字符

`\W` 非字母数字字符

`\S` 非空白字符

`.` 除了newline之外的任意字符

所以你可以使用下面的表达式匹配一个形如01-30-2003 15:20的日期和时间格式。

```javascript
let dateTime = /\d\d-\d\d-\d\d\d\d \d\d:\d\d/;
console.log(dateTime.test("01-30-2003 15:20));
// -> true
console.log(dateTime.test("30-jan-2003 15:20"));
// -> false
```

这看上去很糟糕。表达式的一半都是反斜线，产生了背景噪声使得很难注意到实际的要表达的模式。我们稍后将看到一个改进版本。

这些反斜线代码也可被用在方括号内部。例如，`[\d.]`表示任何数字或者是一个句号字符。但是句号本身，如果在方括号中间，就失去了特殊的含义。其他特殊字符如`+`也是一样。

为了反转字符集，也就是匹配除了集合中列出的那些字符，可以在起始括号后写一个插入字符`^`。
```javascript
let notBinary = /[^01];
console.log(notBinary.test("110010000110110"));
// -> false
console.log(notBinary.test("1100103300"));
// -> true
```

## 重复模式的一部分

我们现在知道如何匹配一位数字。要是我们想匹配一位或多位数字序列呢？

当在正则表达式中放置一个`+`，表明前面的元素可能不止一次出现。因此，`/\d+/`匹配一位或更多位的字符。

```javascript
console.log(/'\d+'/.test("'123'"));
// -> true
console.log(/'\d+'/).test("''");
// -> false
console.log(/'\d*'/.test("'123'"));
// -> true
console.log(/'\d*'/.test("''"));
// -> true
```

`*`有相似的意义，但是也允许模式出现0次。后面跟着*号的元素不会阻止模式匹配，如果找不到合适的文本匹配将会匹配0个实例。

一个`?`标志模式中的一部分是可选的，意味着要么出现一次要么出现0次。下面的例子中，`u`字符允许存在，但是当`u`字符丢失的时候，模式照样匹配。

```javascript
let neighbor = /neighbou?r/;
console.log(neighbor.test("neighbour"));
// -> true
console.log(neighbor.test("neighbor"));
// -> true
```

为了表明模式应该重复一个精确的次数，使用花括号。在一个元素后面放置一个`{4}`，需要这个元素刚好出现四次。也可以为出现的次数指定一个范围：`{2, 4}`意味着元素必须出现至少2次，至多4次。

这是另一个日期时间模式的版本，允许单位数和双位数日，月和小时。也更容易理解。
```javascript
let dateTime = /\d{1, 2}-\d{1, 2}-\d{4} \d{1, 2}:\d{2};
console.log(dateTime.test("1-30-2003 8:45));
// -> true
```

当用花括号时，你也可以通过忽略逗号后面的数字来指定开放的范围。如`{5,}`表示大于等于5次。

## 子表达式分组

为了一次性在超过一个元素上使用`*`或者`+`操作符，你必须使用括号。就后面的操作符而言，用括号包围的正则表达式的一部分算作单个元素。

```javascript
let cartoonCrying = /boo+(hoo+)/i;
console.log(cartoonCrying.test("Boohoooohoohooo"));
// -> true
```

第一个和第二个`+`字符都只对*boo*和*hoo*中的第二个*o*有效。第三个`+`字符对整个组`(hoo+)`有效，匹配类似于这个的一个或多个序列。

表达式末尾的i使得这个正则表达式忽略大小写，允许它匹配输入字符串中的大写*B*，即便是在模式都是小写字母的情况下。

## 匹配和分组

`test`方法是最简单的匹配正则表达式的方式。它只告诉你字符串是否匹配正则表达式模式而没有任何其他信息。正则表达式也有一个`exec`（execute）方法，如果没有匹配发现则返回`null`，否则返回一个含有匹配信息的对象。
```javascript
let match = /\d+/.exec("one two 100");
console.log(match);
// -> ["100"]
console.log(match.index);
// -> 8
```
从`exec`返回的对象有一个`index`属性，告诉我们成功的匹配在字符串的起始位置。除了那个，对象看起来像（并且实际上就是）一个字符串数组，首元素就是匹配的字符串。在之前的例子中，这就是我们寻找的位串序列。

字符串也有一个`match`方法非常类似。
```javascript
console.log("one two 100".match(/\d+/));
// -> ["100"]
```

当正则表达式包含括号包围起来成组的子表达式时，匹配这些组的文本也会展示在数组中。整个匹配总是第一个元素。下一个元素是被第一个组（起始括号在表达式中最先出现的那组）匹配的部分，然后是第二组等等。

```javascript
let quotedText = /'([^']*)'/;
console.log(quotedText.exec("she said 'hello'"));
// -> ["'hello'", "hello"]
```

当一个组根本不匹配时（如后面跟着`?`），那么它在输出数组中的位置将保存`undefined`。相似地，当一个组匹配多次的时候，只有最后一次匹配展示在数组中。

```javascript
console.log(/bad(ly)?/.exec("bad"));
// -> ["bad", undefined]
console.log(/(\d)+/.exec("123"));
// -> ["123", "3"]
```

分组对提取字符串的一部分特别有用。如果我们不仅是想核实是否一个字符串包含日期还想提取出它然后构建一个代表它的对象，我们可以在数字模式周围包裹括号并且直接从`exec`的结果中提取出日期。

但是首先我们说点题外话，我们将讨论JS中内建的代表日期和时间的值的方式。

## Date类

JS有一个标准的代表日期的类，或者不如说是时间点。它被叫做`Date`。如果你简单的用`new`创造一个日期对象，你可以得到当前的日期和时间。

```javascript
console.log(new Date());
// -> Sun Jan 06 2019 17:34:31 GMT+0800 (中国标准时间)
```

你也可以创造一个给定时间的日期对象。
```javascript
console.log(new Date(2009, 11, 9));
// → Wed Dec 09 2009 00:00:00 GMT+0100 (CET)
console.log(new Date(2009, 11, 9, 12, 59, 59, 999));
// → Wed Dec 09 2009 12:59:59 GMT+0100 (CET)
```

JS使用的规范规定月份数字从0开始，然而日子数字从1开始。这令人困惑。小心点。

最后四个参数（小时，分钟，秒和毫秒）可选，并当未被给出时取0。

时间戳以从1970年开始的毫秒数存储。这遵循了Unix time的规范集，大约是在这个时间被发明的。你可以用负数表示1970之前的时间。`getTime`方法返回这个数字。如你所想的一样它很大。

```javascript
console.log(new Date(2013, 11, 19).getTime());
// → 1387407600000
console.log(new Date(1387407600000));
// → Thu Dec 19 2013 00:00:00 GMT+0100 (CET)
```
如果只给`Date`构造器一个参数，那个参数就以毫秒数的意义被对待。你可以得到当前的毫秒数，要么通过创造一个新的`Date`对象并调用它的`getTime`方法，要么调用`Date.now`函数。

日期对象提供如`getFullYear`,`getMonth`,`getDate`,`getHours`,`getMinutes`和`getSeconds`这样的方法来提取日期的某部分。除了`getFullYear`还有`getYear`方法，给定年份减去1900的差值，并且大多时候都没什么用。

在感兴趣的表达式部分周围放置括号，我们现在可以从一个字符串创造日期对象。

```javascript
function getDate(string) {
    let [_, month, day, year] = /(\d{1,2})-(\d{1,2})-(\d{4})/.exec(string);
    return new Date(year, month - 1, day);
}
console.log(getDate("1-30-2003"));
// → Thu Jan 30 2003 00:00:00 GMT+0100 (CET)
```

下划线绑定被忽略了并且只用于跳过exec返回的数组中的完整的匹配的元素。

## 单词和字符串边界

不幸的是，`getDate`将会开心地从字符串*100-1-30000*中提取无意义的日期*00-1-3000*。匹配可能发生在字符串的任意位置，在这个实例中，匹配开始于第二个字符并结束在倒数第二个字符。

如果我们想强制匹配必须横跨整个字符串，我们可以添加`^`和`$`标识。插入符号（^）匹配输入字符串的开头，而`$`符号匹配字符串的末尾。所以`/^\d+$/`只会匹配完全包含一位或多位数字的字符串，而`/^!/`匹配任何以感叹号开头的字符串，`/x^/`不匹配任何字符串（不可能在字符串的起始前面还有一个x）。

如果我们只想确保日期开始和终止于单词边界（word boundary），我们可以使用标识符`\b`。一个单词边界可以是字符串的开始或者结尾，也可以是字符串中任何一边是单词字符（如\w）另一边是非单词字符的点。

```javascript
console.log(/cat/.test("concatenate"));
// -> true
console.log(/\bcat\b/).test("concatenate"));
// -> false
```
注意到一个边界标识符不匹配一个实际的字符。它只是强制正则表达式在某个特定的条件出现的位置时匹配。

## 选择模式

假设我们想知道一段文本是否包含一个数字后面跟着单词*pig*，*cow*，或者*chicken*，或者任何它们的复数形式。

我们可以写三个正则表达式并按顺序特使他们，但是还有个更好的方式。管线操作符（|）表示在左边的模式和右边的模式之间做一个选择。所以可以这样写：
```javascript
let animalCount = /\b\d+ (pig|cow|chicken)s?\b/;
console.log(animalCount.test("15 pigs"));
// -> true
console.log(animalCount.test("15 pigchickens"));
// -> false
```

括号可以被用于限制管线操作符应用的范围，你可以将多个这样的操作紧挨着彼此放置来表达在大于两种可供选择的事物之间的选择。

## 匹配的机制

概念上，如果你使用`exec`或者`test`，正则表达式引擎首先通过尝试从字符串起始匹配表达式，然后是第二个字符等等，在你的字符串中寻找一个匹配，知道找到了一个匹配或者到达字符串末尾。要么返回第一个匹配或者不能找到任何匹配。

为了做实际的匹配，引擎对待正则表达式的方式有点像一个流程图。这是上个例子中牲畜表达式的图。

{% asset_img livestock.PNG %}

如果我们从图的左边到图的右边找到一条路径，我们的正则表达式就会匹配。我们保存字符串中的当前位置，并且每次我们向前穿过一个盒子，我们见擦汗我们当前位置之后的字符串部分是否匹配那个盒子。

所以我们如果从位置4尝试匹配"the 3 pigs"，我们穿越流程图的过程应该像这样：
- 在位置4，有一个单词边界，我们我们可以穿过第一个盒子
- 依然在位置4，我们发现了一个数字，所以我们也能穿过第二个盒子。
- 在位置5，一条路径回到了第二个（数字）盒子之前的位置，而另一条向前穿过保存着一个空白字符的盒子。因为这个字符串有一个空白字符，我们走第二条路径。
- 我们现在到了位置6（pig的起始位置），在图中的第三条分支。我们没有在这里看见*cow*或者*chicken*，但是我们看见了*pig*，所以我们走这个分支。
- 在位置9，在三路分支之后，一条路径跳过了*s*盒子并直接到达最终的单词边界，而另一条分支匹配一个*s*。因为我们这里有一个*s*字符，而不是单词边界，所以我们走*s*盒子。
- 我们现在在位置10（字符串结尾）并且只可以匹配单词边界。字符串结尾可以算作单词边界，所以我们穿过最后一个盒子并成功匹配了这个字符串。

## 回溯

正则表达式`/\b([01]+b|[\da-f]+h|\d+)\b/`要么匹配一个二进制数后面跟着b，要么是一个十六进制数后面跟着h，要么是一个十进制数没有后缀。这是对应的图。

{% asset_img backtrack.png %}

当匹配这个表达式时，经常发生这样的情况，即便输入实际上不包含一个二进制数，上面的二进制分支也会进入。当匹配字符串"103"时，只有到了3我们才知道我们进入了错误的分支。这个字符串确实可以匹配这个表达式，只是不是我们当前所在的分支。

所以匹配程序回溯。当进入一个分支时，它记得当前的位置（在这个情况下，在字符串的起始，刚好经过图中的第一个边界盒子）所以如果当前分支不可行可以回溯并且尝试另一个分支。对于字符串"103"，在遇到字符3之后，将会开始尝试十六进制数的分支，因为数字之后没有*h*，所以匹配会再次失败。所以会尝试十进制分支。这一个匹配了，并且最终报告了一个匹配。

一遇到一个完整的匹配匹配程序就会终止。这意味着如果多个分支可能潜在的匹配字符串，但只有第一个分支（以正则表达式中的分支序为准）会被使用。

回溯也发生在重复操作符如`+`和`*`。如果你匹配`/^.*x/`和"abcxe"，这个`.*`部分首先尝试消耗整个字符串。引擎然后意识到他需要一个x来匹配模式。因为字符串末尾没有一个x，所以`*`操作符尝试去掉一个字符匹配。但是匹配程序在`abcx`之后依然没有发现x，所以继续回溯，将abc与`*`操作符进行匹配。现在他发现了一个需要的x并且从位置0到4报告了一次成功的匹配。

写一个要做很多回溯的正则表达式是可能的。当一个模式可以以许多不同的方式去匹配一部分输入的时候这个问题就会发生。例如，如果我们写二进制数正则表达式的时候比较迷糊，我们可能意外的写类似于这样的`/([01]+)+b/`。

{% asset_img many.png %}

如果尝试匹配非常长的01序列并且末尾没有b字符，匹配程序首先经过内循环直到用完所有数字。然后它意识到没有b，所以回溯一个位置。经过一次外循环，并且再次放弃，尝试去再一次回溯内循环。通过这两个循环将继续尝试所有可能的路线。这意味着每一个额外的字符都使工作量翻倍。即便是几十个字符，导致的匹配实际上也需要很长时间。

## replace方法

字符串值有一个`replace`方法，可被用于替换掉字符串中的一部分。
```javascript
console.log("papa".replace("p", "m"));
// -> mapa
```
第一个参数也可以是一个正则表达式，在这种情况下第一个匹配到正则表达式的部分被替换。当一个`g`(global)选项被添加到正则表达式时，所有字符串中的匹配都会被替换，而不仅是第一个。
```javascript
console.log("Borobudur".replace(/[ou]/, "a"));
// -> Barobudur
console.log("Borobudur".replace(/[ou]/g, "a"));
// -> Barabadar
```
如果替换一个匹配或者替换所有匹配通过`replace`方法的额外参数或者通过提供一个不同的`replaceAll`方法来实现可能是更明智的。但是因为某些不幸的原因，这个选择依赖于正则表达式的属性。

使用正则表达式进行`replace`的真正强大之处在于我们可以在替换字符串中引用匹配到的组。例如，你假设我们有一个包含人名的大的字符串，每一行一个名字，以*Lastname, Firstname*的格式。如果我们想交换这些名字并移除逗号去得到一个*Firstname Lastname*这样的格式，我们可以使用如下代码：
```javascript
console.log("Liskov, Barbara\nMcCarthy, John\nWadler, Philip".replace(/(\w+), (\w+)/g, "$2 $1"));
// -> Barbara Liskov
//    John McCarthy  
//    Philip Walder  
```
在替换字符串中的`$1`和`$2`引用模式中括号括起来的组。`$1`被匹配到第一组的文本替换，`$2`被匹配到第二组的替换，依此类推，直到`$9`。整个匹配可以通过`$&`引用。

可以将函数作为`replace`的第二个参数传递进来。对于每一个替换，函数将会将每个匹配的组（也包括整个匹配）作为参数进行调用，得到的返回值被插入到新的字符串。这是一个小例子。
```javascript
let s = "the cia and fbi";
console.log(s.replace(/\b(cia|fbi)\b/g, str => str.toUpperCase()));
// -> the CIA and FBI
```
这个更有趣：
```javascript
let stock = "1 lemon, 2 cabbages, and 101 eggs";
function minusOne(match, amount, unit) {
    amount = Number(amount) - 1;
    if(amount == 1) { // only one left, remove ths 's'
        unit = unit.slice(0, unit.length - 1);
    } else if(amount == 0) {
        amount = "no";
    }
    return amount + " " + unit;
}
console.log(stock.replace(/(\d+) (\w+)/g, minusOne));
// -> no lemon, 1 cabbage, and 100 eggs
```
这里创造了一个字符串，寻找所有数字后面跟着字母数字的单词的出现，并返回一个字符串，其中每个出现都减1。

这个`(\d+)`组最终作为函数的`amount`参数，`(\w+)`组绑定到`unit`上。函数将`amount`转换为数字，因为它匹配`\d+`所以总是可行的。并且做一些调整以防只有0或者1剩下。

## 贪婪

使用`replace`写一个函数从JS中移除所有的注释是可能的。这是第一个尝试：
```javascript
function stripComments(code) {
    return code.replace(/\/\/.*|\/\*[^]*\*\//g, "");
}
console.log(stripComments("1 + /* 2 */3"));
// → 1 + 3
console.log(stripComments("x = 10;// ten!"));
// → x = 10;
console.log(stripComments("1 /* a */+/* b */ 1"));
// → 1  1
```

在`or`操作符之前的部分匹配两个正斜线后面跟着任何非换行字符（.匹配除换行符之外的其他字符）。多行注释的部分有点复杂。我们使用`[^]`(任何不在字符空集中的字符)作为一种方式去匹配任意字符。因为块级注释可以包含多行，句号不匹配换行符，所以我们不能只用一个`.`在这。

但是最后一行的输出显然是错误的。为什么？

表达式的`[^]*`部分，如同我在回溯部分描述的那样，将首先尽可能多的匹配。如果导致了模式后面的部分匹配失败，匹配程序才会回退一个字符并从那里再次尝试。在这个例子中，匹配程序首先尝试匹配字符串的整个的剩余部分并且从那里回退。在回退了4个字符之后它发现了`*/`的出现并且匹配了它。这并不是我们想要的结果，我们的目的是匹配单个注释，而不是一直到代码的结尾找到最后一个块级注释的结尾。

由于这种行为，我们说重复操作符（+, *, ?和{}）是贪婪的，意味着它们会尽可能多的匹配然后再进行回溯。如果你在他们之后放置一个问号（+?, *?, ??, {}?）。它们就会成为非贪婪地并尽可能少的匹配，只在剩余的模式不匹配时才会增加匹配的字符。

那就是我们这里想要的。通过让`*`匹配最少的带我们到`*/`的字符，我们仅仅消耗了一个块级注释。
```javascript
function stripComments(code) {
    return code.replace(/\/\/.*|\/\*[^]*?\*\//g, "");
}
console.log(stripComments("1 /* a */+/* b */ 1"));
// -> 1 + 1
```

许多再正则表达式程序中的错误都可以归因为无意地在本该使用非贪婪操作符的时候使用了贪婪操作符。当使用重复操作符的时候，首先考虑非贪婪的变种。

## 动态创造正则表达式对象

当你写代码时你可能不知道准确的你要匹配的模式。假设你想要在一段文本中寻找用户的名字并且用下划线字符包围它来使它突出。因为只有在程序实际运行的时候你才知道名字，你就不能使用基于正斜线的记号。

但是你可以构建一个字符串并使用`RegExp`构造器。这是一个例子：
```javascript
let name = "harry";
let text = "Harry is a suspicious character.";
let regexp = new RegExp("\\b(" + name + ")\\b", "gi");
console.log(text.replace(regexp, "_$1_"));
// → _Harry_ is a suspicious character.
```

当创建`\b`边界标识符时，我们不得不使用两个反斜线，因为我们在普通字符串中书写他们而不是在正斜线包裹的正则表达式中。`RegExp`的第二个参数包含了正则表达式的选项，在刚刚的例子中，"gi"代表全局（global）和大小写不敏感（insensitive）。

但是要是名字是"dea+hl[]rd"呢？这将导致一个荒谬的正则表达式，实际上不会匹配用户的名字。

为了解决这个，我们需要在任何有特殊意义的字符前加上反斜线。
```javascript
let name = "dea+hl[]rd";
let text = "This dea+hl[]rd guy is super annoying.";
// 这里为什么只对圆括号的右半边做特殊转义，而中括号和大括号不用？？？？？
let escaped = name.replace(/[\\[/+*?(){|^$]/g, "\\$&");
let regexp = new RegExp("\\b" + escaped + "\\b", "gi");
console.log(text.replace(regexp, "_$&_"));
// -> This _dea+hl[]rd_ guy is super annoying.
```

## 搜索方法

字符串的`indexOf`方法不能够使用正则表达式调用。但是有一个`search`方法可用于正则表达式。类似`indexOf`，返回表达式出现的位置，如果没有找到则返回-1。
```javascript
console.log("  word".search(/\S/));
// -> 2
console.log("    ".search(/\S/));
// -> -1
```

不幸的是，没有办法指示匹配从某偏移位置开始（像`indexOf`的第二个参数一样）。

## `lastindex`属性

`exec`方法类似地，没有提供一个便捷的从字符串某位置开始查找的方式。但是有一种不太方便的方式。

正则表达式对象具有属性。其中一个就是`source`属性，指明表达式源自于哪个字符串。另一个属性就是`lastIndex`，在一些限制条件下，控制下一次匹配开始的位置。

条件就是正则表达式必须开启全局（g）或者粘性（y）选项，并且匹配必须通过`exec`方法发生。再一次的，要是允许一个额外的参数传递给`exec`，可能就不那么让人困惑，但是使人困惑就是JS正则表达式接口的一个必要的特性。
```javascript
let pattern = /y/g;
let match = pattern.exec("xyzzy");
console.log(match.index);
// -> 4
console.log(pattern.lastIndex);
// -> 5
```
如果匹配成功，那么对`exec`的调用自动改变`lastIndex`属性去指向匹配之后的点。如果没有匹配，那么`lastIndex`被设置为0，也就是新构建的正则表达式对象中的值。

全局（g）和粘性（y）选项的区别在于：当粘性（sticky）开启时，只有当匹配直接开始于`lastIndex`匹配才会成功，然而对于全局选项而言，将会向前找到一个匹配可以开始的位置。
```javascript
let global = /abc/g;
console.log(global.exec("xyz abc"));
// -> ["abc"]
let sticky = /abc/y;
console.log(sticky.exec("xyz abc"));
// -> null
```

当从多个`exec`调用中使用一个共享的正则表达式时，这些对`lastIndex`的自动改变可能导致问题。你的正则表达式可能开始于上次调用遗留下来的位置。
```javascript
let digit = /\d/g;
console.log(digit.exec("here it is: 1"));
// -> ["1"]
consoel.log(digit.exec("and now: 1"));
// -> null
```

全局选项另一个有趣的效果就是它改变了`match`方法在字符串上的工作方式。当用一个全局正则表达式调用时，代替返回一个类似于从`exec`方法返回的数组，`match`将找到所有字符串中的模式匹配并返回一个包含匹配字符串的数组。
```javascript
console.log("Banana".match(/an/g))
```
所以对于全局正则表达式要格外小心。调用`replace`和你想要明确使用`lastIndex`的时候，是典型的唯一的你需要使用全局选项的地方。

## 在匹配上循环

扫描字符串中所有模式的出现是很常做的一件事，以在循环体中获取到匹配对象的方式实现。我们可以使用`lastIndex`和`exec`来实现。

```javascript
let input = "A string with 3 numbers in it... 42 and 88.";
let number = /\b\d+\b/g;
let match;
while (match = number.exec(input)) {
    console.log("Found", match[0], "at", match.index);
}
// → Found 3 at 14
//   Found 42 at 33
//   Found 88 at 40
```

这利用了赋值表达式的值就是被赋的值的事实。所以通过使用`match = number.exec(input)`作为`while`循环的条件，我们从每次迭代开始执行匹配，将结果存放到一个绑定中，并在没有多余匹配的时候停止循环。

## 解析一个ini文件

为了结束这个章节，我们将研究一个需要正则表达式的问题。假设我们正在写一个程序去自动收集关于互联网上我们敌人的信息。（我们实际上不会在这里写这个程序，仅仅是读取配置文件的部分）。配置文件看起来像这样：

```
searchengine=https://duckduckgo.com/?q=$1
spitefulness=9.7

; comments are preceded by a semicolon...
; each section concerns an individual enemy
[larry]
fullname=Larry Doe
type=kindergarten bully
website=http://www.geocities.com/CapeCanaveral/11451

[davaeorn]
fullname=Davaeorn
type=evil wizard
outputdir=/home/marijn/enemies/davaeorn
```

这个格式（广泛被使用，通常叫做INI文件）的规则如下：
- 空行和开始于分号的行被忽略
- 包裹在[]内的行开始一个新的section
- 包含数字字母的标识符后面跟着一个等号字符为当前section添加一个设定
- 任何其他东西都不合法
我们的任务是转换这样的字符串到一个对象，其属性包含在第一个section头之前的设定字符串和section的子对象，这些子对象包含section的设定。

因为这个格式必须一行挨一行的处理，将整个文件分隔为独立的行是一个好的开端。我们在第四章见过split方法。一些操作系统不使用换行符去分隔行，而是用回车换行字符（"\r\n")。`split`方法也能接受一个正则表达式，我们就可以使用像`/\r?\n/`去分隔行，并同时允许两种换行方式的存在。
```javascript
function parseINI(string) {
    let result = {};
    let section = result;
    string.split(/\r?\n/).forEach(line => {
        let match;
        if(match = line.match(/^(\w+)=(.*)$/)) {
            section[match[1]] = match[2];
        } else if (match = line.match(/^\[(.*)\]$/)) {
            section = result[match[1]] = {};
        } else if(!/^\s(;.*)?$/.test(line)) {
            throw new Error("Line '" + line + "' is not valid.");
        }
    });
    return result;
}

console.log(parseINI(`
name=Vasilis
[address]
city=Tessaloniki`));
// → {name: "Vasilis", address: {city: "Tessaloniki"}}
```

代码遍历文件的行然后构建一个对象。顶部属性直接存储在对象中，而section中的属性存储在独立的section对象中。`section`绑定指向当前section对象。

有两种值得注意的行，section头部或者属性行。当某行是一个常规的属性，就被存储在当前的section。当它是一个section头时，一个新的section对象被创建，并且`section`被设置为指向它。

注意反复使用的`^`和`$`是为了确保整行匹配而不是部分匹配。如果不考虑这些问题，代码的大部分能够正常工作，但是对于某些输入也许会表现得不正常，可能是一个很难追踪的bug。

模式`if(match = string.match(...))`类似于在`while`循环条件中使用赋值的小窍门。你经常不确定对`match`的调用是否成功，所以可以在if语句中获取结果对象来测试。为了不打破`else if`的链式形式，我们将匹配的结果赋给一个绑定，然后立刻使用那个赋值作为if的判定条件。

如果一行不是一个section的头或者一个属性，函数用`/^\s*(;.*)?$`来检查是否是一行注释或者一个空行。你发现它是如何工作的了么？在圆括号中间的部分匹配注释，而`?`确保它也可以匹配只包含空白字符的行。当某行不匹配任何期望的形式，函数抛出异常。

## 国际化字符

由于JS最初的简化实现以及这种简化实现后来被固定为标准行为的事实，JS的正则表达式对于非英语字符表现得相当愚蠢。例如，就JS的正则表达式而言，一个单词字符仅仅包含拉丁字母表中的26个英文字母（大写或小写），数字和下划线（由于某些原因）。类似于é和β，绝对是单词字符，但是不匹配`\w`（将匹配`\W`，非单词字符分类）。

一个奇怪的历史巧合，`\s`（空白字符）没有这个问题并且匹配Unicode标准中认为是空白字符的所有字符，包括像不间断空格（nonbreaking space）和蒙古语元音分隔符（Mongolian vowel separator）这样的字符。

另一个问题是，默认地，正则表达式处理代码单元，如同第五章讨论的那样，并不是实际的字符。这意味着由两个代码单元构成的字符表现得很奇怪。
```javascript
console.log(/🍎{3}/.test("🍎🍎🍎"));
// → false
console.log(/<.>/.test("<🌹>"));
// → false
console.log(/<.>/u.test("<🌹>"));
// → true
```
问题是第一行的🍎被认为是两个代码单元，`{3}`部分只应用到第二个苹果。相似地，点操作符只匹配一个胆码单元，并非组成玫瑰emoji的两个代码单元。

你必须为你的正则表达式添加一个`u`选项（Unicode）使得他正确地对待这些字符。不幸的是错误的行为保持和从前一样，因为改变这个会导致依赖它的代码出现问题。

虽然这只是标准化的并且在写作本文的时候，还没有广泛地被支持，在正则表达式中使用`\p`（必须开启unicode选项）去匹配Unicode标准分配一个给定属性(property)的所有字符。

```javascript
console.log(/\p{Script=Greek}/u.test("α"));
// -> true
console.log(/\p{Script=Arabic}/u.test("α"));
// -> false
console.log(/\p{Alphabetic}/u.test("α"));
// -> true
console.log(/\p{Alphabetic}/u.test("!"));
// -> false
```
Unicode定义了一些有用的属性（property），即使发现一个你需要使用的可能不总是容易的。你可以使用`\p{Property=Value}`标记来匹配对于这个属性这个值的任何字符。如果属性名没给定，如`\p{name}`，名字被假定为要么是一个二进制（binary）属性如`Alphabetic`或者一个分类如`Number`。

## 总结

正则表达式就是表示字符串中模式的对象。它们使用自己的语言表达这些模式。

- `/abc/`: 一个字符序列
- `/[abc]/`: 从一组字符中选择一个字符
- `/[^abc]/`: 任何不再字符集合中的字符
- `[0-9]`: 任何在字符范围中的字符
- `/x+/`: 模式x一次或者多次出现
- `/x+?/`: 非贪婪地匹配一次或者多次出现
- `/x*/`: 0次或多次出现
- `/x?/`: 0次或1次出现
- `/x{2, 4}/`: 2到4次出现
- `/(abc)/`: 一组
- `/a|b|c/`: 几个模式中任何一个
- `/\d/`: 任何数字字符
- `/\w/`: 一个字母数字字符（单词字符）
- `/\s/`: 任何空白字符
- `/./`: 除了换行符之外的任意字符
- `/\b/`: 一个单词边界
- `/^/`: 输入的起始
- `/$/`: 输入的终止

正则表达式有一个`test`方法去测试是否一个给定的字符串匹配它。它也有一个`exec`方法，当匹配发生的时候，返回一个包含所有匹配的组的数组。这样的数组有一个`index`属性，表明匹配开始的地方。

字符串有一个`match`方法去将他们和正则表达式匹配，还有一个`search`方法去搜索一个匹配，并只返回匹配开始的位置。它们的`replace`方法可以用一个替代字符串或一个函数来代替匹配到的模式。

正则表达式可以有选项，在闭合正斜线后书写。`i`选项让匹配不区分大小写。`g`选项使得表达式进行全局匹配，对于字符串的`replace`方法使得它替换掉所有匹配的字符串。`y`选项使得它成为粘性的，意味着当寻找匹配时不会向前搜索并且跳过字符串的一部分。`u`选项开启`Unicode`模式，在处理占据两个代码单元的字符上，修复了一系列问题。

正则表达式是一种难于对付的锐利工具。它们极大简化了某些任务但是被应用到复杂问题时很快就变得难以管理。知晓如何使用它们的一部分就是抵制硬塞进它们不能清楚表达的东西的冲动。

## 练习

不可避免地在做这些练习的时候，你会感觉困惑和沮丧，因为一些正则表达式令人费解的行为。有时将表达式输入一个类似 https://debuggex.com 的在线工具去观察是否可视化对应你所期望的那样并且尝试它对各种输入字符串的响应方式是有帮助的。

### 正则高尔夫

*Code golf*是尽可能少的使用字符来表达一个特定的程序的的游戏的术语。相似地，*regexp golf*就是练习写尽可能小的正则表达式去匹配一个给定的模式，并只匹配那个模式。

对于下面每一个条目，写一个正则表达式去测试是否任何给定的子串在一个字符串中。正则表达式应该只匹配描述的子串之一。除非明确指定否则不要担心单词边界。当你的表达式工作的时候，看看是否能使得它变得更简洁。

1. car and cat

2. pop and prop

3. ferret, ferry, and ferrari

4. Any word ending in ious

5. A whitespace character followed by a period, comma, colon, or semicolon

6. A word longer than six letters

7. A word without the letter e (or E)

请参考章节总结。用一些测试字符串去测试每个答案。

```javascript
// Fill in the regular expressions

// 还可以写ca(r|t)
verify(/ca[rt]/,
       ["my car", "bad cats"],
       ["camper", "high art"]);

verify(/pr?op/,
       ["pop culture", "mad props"],
       ["plop", "prrrop"]);

verify(/^ferr(et|y|ari)$/,
       ["ferret", "ferry", "ferrari"],
       ["ferrum", "transfer A"]);

verify(/ious\b/,
       ["how delicious", "spacious room"],
       ["ruinous", "consciousness"]);

// 还可以写/\s[.,:;]/
verify(/\s\.|,|:|;/,
       ["bad punctuation ."],
       ["escape the period"]);

verify(/\w{7}/,
       ["hottentottententen"],
       ["no", "hotten totten tenten"]);

// 任意单词都可以，限定一个单词边界然后必须是单词字符非e。
verify(/\b[^\We]+\b/i,
       ["red platypus", "wobbling nest"],
       ["earth bed", "learning ape", "BEET"]);


function verify(regexp, yes, no) {
  // Ignore unfinished exercises
  if (regexp.source == "...") return;
  for (let str of yes) if (!regexp.test(str)) {
    console.log(`Failure to match '${str}'`);
  }
  for (let str of no) if (regexp.test(str)) {
    console.log(`Unexpected match for '${str}'`);
  }
}
```
### 引用风格

假设你写了一个故事，并自始至终使用单引号标记对话。现在你想用双引号替换掉所有的单引号，同时保留缩写在*aren't`中的单引号。

考虑一个模式区分这两种引号使用并且精心制作一个对`replace`的调用来做合适的替换。

```javascript
let text = "'I'm the cook,' he said, 'it's my job.'";
// Change this call.
console.log(text.replace(/(^|\W)'|'(\W|$)/g, '$1"$2'));
// → "I'm the cook," he said, "it's my job."
```

这个题有点意思，记录下查看了solution之后的想法。首先我们分析这两种引号的差异，对于不需要做变化的引号，左右肯定都是单词字符，而对于需要替换的引号，肯定要么左边是非单词字符或者右边是非单词字符，或者位于起始和结束位置的引号。所以才有了上面的正则表达式。

### 再一次数字

写一个只匹配JS风格的数字的表达式。在数字前必须支持可选的加号或者减号，小数点和指数记号（如5e-3或者1E10），在指数前面有一个可选的正负号。同时注意没有必要在小数点前或者后有数字，但是数字不能够只有一个点。也就是`.5`和`5.`是合法的JS数字，但是单独的点不是。

```javascript
// Fill in this regular expression.
let number = /^[+-]?(\d+(\.\d*)?|\.\d+)([eE][+-]?\d+)?$/;

// Tests:
for (let str of ["1", "-1", "+15", "1.55", ".5", "5.", "1.3e2", "1E-4", "1e+12"]) {
  if (!number.test(str)) {
    console.log(`Failed to match '${str}'`);
  }
}
for (let str of ["1a", "+-1", "1.2.3", "1+1", "1e4.5", ".5.", "1f5", "."]) {
  if (number.test(str)) {
    console.log(`Incorrectly accepted '${str}'`);
  }
}
```

这个模式感觉有点费解。正负号首先是可选的，然后e之前的部分，这部分允许我们是一个小数或者一个整数，小数可以以`.5`或者`5.`形式出现。那么我们这里对于符号自然是选择利用重复操作符，即可选操作符`?`。然后对于下面的部分就有意思了，我们将带有整数部分的小数和不带整数部分的小数分开来看。带有整数的小数需要整数部分必定大于等于1位，并且小数部分可选。对于不带整数部分的小数，小数部分至少一位。而后面的指数部分整体可选，采用`?`标识。而如果存在，则必然以e或者E开头，然后是可选的正负号，然后才是至少一位长的数字。