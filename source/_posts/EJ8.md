---
title: EJ8
date: 2019-01-04 11:44:04
categories:
- Eloquent JavaScript
---

# BUGs和ERRORs

程序中的缺陷叫做bugs。如果一个程序是思想结晶的，可以将bug大致分为由困惑的想法导致的和由当将想法转换为代码时引入的错误。前者通常比后者更难诊断和修复。

<!-- more -->

## 语言

如果计算机知道足够多的信息关于我们要做的事情，那么许多错误可以自动被计算机指出。但是JS的松散性是一个障碍。它绑定和属性的概念是很模糊的以致于在运行程序之前几乎不会捕捉到拼写错误。即便是这样，它不带抱怨的允许你做一些明确无意义的事情，比如计算`true * "monkey"`。

不过还是有一些JS抱怨的东西。写一个不遵循JS语法的程序将使得计算机立刻做出抱怨。其他如调用一个非程序或者在underfined的值上查找一个属性，当程序尝试去执行行为的时候会导致一个报错。

但是通常情况下，你的无意义的计仅仅产生`NaN`或者一个`underfined`值，程序还是会继续快乐地进行，确信它自己正在做有意义的事情。这个伪值可能经过几个函数之后，错误才会出现。他可能不会触发错误，而只是静悄悄地使得程序输出是错误的。找到这样问题的源头有点困难。

寻找bugs的过程叫做*dubugging*

## 严格模式

JS通过开启严格模式可以变得更严格。通过在文件或者函数体顶部放一个`use strict`实现。

```javascript
function canYouSpotTheProblem() {
  "use strict";
  for (counter = 0; counter < 10; counter++) {
    console.log("Happy happy");
  }
}

canYouSpotTheProblem();
// → ReferenceError: counter is not defined
```

一般地，当忘记了在绑定前加一个`let`，如同例子中的`counter`这样，JS静默的创造一个全局绑定。相反地，严格模式下会报错。这是很有用的。即便这样，如果例子中的绑定一个有一个全局绑定的时候这种方法是无效的。在那种情况下，循环将悄悄覆盖绑定的值。

在严格模式下，另一个变化是当函数没有被当作方法调用时，函数中的`this`绑定为`underfined`。当在严格模式外做这样的调用时，`this`指的是全局作用域。所以如果在严格模式下意外地错误地调用一个方法或者构造器，一旦尝试从`this`中读取什么的时候将立刻产生一个错误，而不是写入全局作用域。

例如，考虑下面的代码，没有使用`new`关键字调用构造器函数，所以这个`this`将不会指代新创建的对象。
```javascript
function Person(name) { this.name = name; }
let ferdinand = Person("Ferdinand"); // oops
console.log(name);
// → Ferdinand
```
所以这个对构造器`Person`的伪调用成功了，但是返回了一个`undefined`值并且创造了一个全局绑定`name`。在严格模式下，结果是不同的。

```javascript
"use strict"
function Person(name) { this.name = name; }
let ferdinand = Person("Ferdinand"); // forgot new
// → TypeError: Cannot set property 'name' of undefined
```
我们被立刻告知有什么东西出错了，这是很有用的。

幸运的是，以`class`标记创造的构造器如果不用`new`调用将总是会抱怨，使得即便不是在严格模式下这也不成问题。

严格模式可以做更多的事情。它不允许给定一个函数多个同名的参数并完全移除了一些有问题的语言特性（如`with`语句）

简言之，将`use strict`放在程序顶部无伤大雅并且帮助你发现问题。

## 类型

一些语言在运行程序之前想要直到所有的绑定和表达式的类型。当一个类型以一种不一致的方式使用的时候将会立刻告诉你。JS只在实际运行程序的时候才考虑类型，并且即使在运行实际程序时，也经常尝试隐式转换值到它期望的类型，所以这没有多大帮助。

然而，类型提供了一个讨论程序的有用的框架。许多错误来自于对函数接收和输出的值的类型的困惑。如果将这些信息写下，可能就不那么容易困惑。

你可以在前面章节的`goalOrientedRobot`函数前添加下面的注释来描述它的类型。

```javascript
// (VillageState, Array) → {direction: string, memory: Array}
function goalOrientedRobot(state, memory) {
  // ...
}
```

有许多不同的规范用类型注解JS的程序。

关于类型的一件事情是，它们需要引入自己的复杂度以便能够描述足够有用的代码。你认为返回数组中随即元素的`randomPick`函数的参数和返回值类型应该是什么呢？你需要引入一个类型变量`T`，可以代表任何类型，所以可以给`randomPick`一个类似于`([T]) -> T`的类型（从Ts数组到T的函数）。

当程序的类型已知的时候，计算机为你检查类型是可能的，在程序运行之前指出错误。有许多JS方言添加了类型到语言中并且检查它们。最受欢迎的是TS，如果你对添加更多严格感兴趣，我推荐你尝试一下。

这本书中，我们将继续使用原生，危险，无类型的JS代码。

## 测试

如果语言不能够在帮助我们发现错误上做很多事情，我们将不得不以很艰难的方式找到它们：通过运行程序然后观察是否做正确的事情。

重复地手工操作这个真的是一个坏主意。不仅很讨厌，而且每次改变程序的时候都要测试所有事情实在是很低效。

计算机擅长做重复的任务，并且测试是一个理想的重复的任务。自动化测试是编写一个程序测试另外一个程序的过程。书写测试需要比手动测试做更多的更多的工作，但是一旦完成，就收获了超能力：只需要几秒钟就可以确认在测试所针对的情况里，你的程序是否表现得正常。当打破什么东西的时候，你将会立刻注意到，而不是在之后的某个时间里，随机的碰见这个问题。

测试通常采用小的标记程序的方式验证代码的某些方面。例如，一组针对`toUpperCase`的测试可能看起来是这样的：
```javascript
function test(label, body) {
  if (!body()) console.log(`Failed: ${label}`);
}

test("convert Latin text to uppercase", () => {
  return "hello".toUpperCase() == "HELLO";
});
test("convert Greek text to uppercase", () => {
  return "Χαίρετε".toUpperCase() == "ΧΑΊΡΕΤΕ";
});
test("don't convert case-less characters", () => {
  return "مرحبا".toUpperCase() == "مرحبا";
});
```

书写类似这样的测试可能产生重复的尴尬的代码。幸运的是，存在帮助你构建和运行测试集的软件，这种软件通过提供一种合适的语言（以函数和方法形式）去表达测试并且当测试失败的时候输出提示性的信息。这些通常被叫做测试运行器（*test runner*)。

一些代码相比于其他代码更容易测试。通常来说，代码交互的外部对象越多，建立测试的上下文就越困难。前面章节的编程风格，使用了自包含的持久的值而不是变化的对象，比较容易去测试。

## Debugging

一旦由于程序表现异常或者产生错误，你发现你的程序产生了问题，下一步就是找到问题在哪里。

有时很显然。错误信息将会指出程序特定的一行，并且如果查看错误描述和对应行的代码，你通常可以发现问题。

但是不总是这样。有时触发问题的原因仅仅是在某处产生的奇怪的值被以一种不合法的方式使用了。

下面的例子程序，通过重复取得最后一位然后划分这个数字去除最后一位，将一个整数转换为一个给定基数的字符串。但是这个奇怪的输出表明有个bug。

```javascript
function numberToString(n, base = 10) {
  let result = "", sign = "";
  if (n < 0) {
    sign = "-";
    n = -n;
  }
  do {
    result = String(n % base) + result;
    n /= base;
  } while (n > 0);
  return sign + result;
}
console.log(numberToString(13, 10));
// → 1.5e-3231.3e-3221.3e-3211.3e-3201.3e-3191.3e-3181.3…
```

即使你已经发现了问题，也要假装你并没有发现。我们知道我们的程序出故障了，并且想要知道原因。

在这种情况下，你必须克制强烈的欲望去随即改变代码然后观察是否那使得它变得更好。相反，*think*。分析发生了什么并且想出发生这种现象的可能的原因。然后，做额外的观察去测试这个理论。或者，如果你还没有一套理论，做额外的观察去帮助你想出一个。

放置一些策略上的`console.log`调用到程序中，是一个查看程序中发生了什么的好的方式。在这种情况下，我们想要`n`接收值13和1，然后是0。让我们在循环开始写下它的值。
```javascript
13
1.3
0.13
0.013
…
1.5e-323
```

13除以10不会产生一个整数。我们实际想要的是`n = Math.floor(n / base)`而不是`n /= base`，如此一来这个数就能够恰当地向右移位。

用`console.log`去观察程序行为的一个替代就是浏览器的*debugger`能力。浏览器可以在代码特定的某一行设置一个断点，并且可以检查绑定的值在那个点上。我不会涉及太多细节，因为不同浏览器的debugger都不太相同，可以在开发者工具或者在网上搜索更多信息。

另外一种设置断点的方式是在你的程序中包含一个`debugger`语句（简单包含这个关键字）。如果浏览器的开发者工具激活了，程序将会停在这个语句。

## 错误传播（error propagation）

并非所有的问题都可以被编程者避免。如果你的程序以某种方式和外界进行通信，就可能出现输入格式不正确，负载过重或者是网络故障的情况。

如果你是为自己编程，可以忽略这些问题直到它们出现。但是如果你需要构建要被他人使用的东西，你通常想要程序表现得更好而不是崩溃。有时跳过错误的输入并继续运行是正确的要做的事情。在其他情况下，报告给用户哪里出错并且然后放弃。但是在任何一种情况下，程序不得不积极地做一些事情响应问题。

假设你有一个函数`promptInteger`，向用户请求一个整数并返回它。如果用户输入"orange"，它应该返回什么？

一个选择是使它返回一个特殊值。常规的这样的值是`null`，`undefined`或者-1。

```javascript
function promptNumber(question) {
    let result = Number(prompt(question));
    if(Number.isNaN(result)) return null;
    else return result;
}

console.log(promptNumber("How many trees do you see?"));
```

现在任何调用`promptNumber`的代码必须检查是否实际的数字被读取，并且如果失败，需要以某种方式恢复，可能是再次询问或者填充一个默认值。或者也可以返回给调用者一个特殊值，用来表明它没能成功做成它被要求的事情。

在许多情况下，主要是错误很普遍的时候，并且调用者应该明确考虑它们，返回一个特殊值是一种好的表明错误的方式。它确实也有缺点。首先，要是函数可以返回任何可能的值呢？在这样的函数中，你将不得不采取一些措施如将结果包装在一个对象中，以此来区分成功还是失败。

```javascript
function lastElement(array) {
    if(array.length == 0) {
        return {failed: true};
    } else {
        return {element: array[array.length - 1]};
    }
}
```

返回特殊值的第二个问题是他可能导致尴尬的代码。如果一段代码调用`promptNumber`10次，就不得不检查10次是否`null`被返回。并且如果对于查找`null`只是简单返回`null`，那么调用者反过来又要检查它。

## 异常

当一个函数不能继续正常执行下去的时候，我们想要做的是停止我们正在做的事情然后立刻跳到一个地方，这个地方知道如何处理这个问题。这就是异常处理所做的事情。

异常是一种使得遇到问题的代码抛出（raise/throw）异常成为可能的一种机制。异常可以是任意值。抛出一个异常有点像从一个函数超动力的返回：不仅跳出当前函数还会跳出它的调用者，一路向下直到第一个开启当前执行的调用。这叫做栈展开（unwinding stack）。你可能记得第三章提到过的函数栈。

如果异常总是向下一直到栈底，那么它们可能没有太多用途。它们只会提供一种新颖的方式去破坏你的程序。它们的强大之处在于，你可以沿着栈设置障碍去捕获异常。一旦你捕获到了异常，你就可以做点什么事情去解决这个问题，然后继续运行程序。

这是一个例子。
```javascript
function promptDirection(question) {
  let result = prompt(question);
  if (result.toLowerCase() == "left") return "L";
  if (result.toLowerCase() == "right") return "R";
  throw new Error("Invalid direction: " + result);
}

function look() {
  if (promptDirection("Which way?") == "L") {
    return "a house";
  } else {
    return "two angry bears";
  }
}

try {
  console.log("You see", look());
} catch (error) {
  console.log("Something went wrong: " + error);
}
```

`throw`关键字被用于抛出一个异常。捕获异常通过将一段代码包裹在`try`块中实现，后面跟着关键字`catch`。当`try`块中的代码导致抛出一个异常，`catch`块就会执行，括号中的名字绑定到异常值。在`catch`块完成之后，或者如果`try`块没有异常的完成了，程序将继续执行整个`try/catch`块后面的语句。

上面的例子中，我们使用了`Error`构造器去创造了一个异常值。这是一个标准JS构造器创造一个带有`message`属性的对象。在大多数的JS环境中，这个构造器的实例也聚集了关于异常创建时存在的调用栈信息，所谓的堆栈追踪（stack trace）。这个信息存储在`stack`属性中并且当试着去debug的时候可能是有用的：它告诉我们问题发生的函数和这个做了失败的调用的函数。

注意`look`函数完全忽略了`promptDirection`可能会出错的可能性。这是异常的一个大的优点：错误处理的代码只在错误发生的地方和要处理的地方才是必须的。处于中间位置的函数完全可以忽略。

嗯，大多数情况下是这样。

## 异常后的清理工作

异常的效果是另外一种控制流。每种可能导致异常的行为（几乎是每个函数调用和属性访问），可能导致控制突然离开你的代码。

这意味着当代码有几个副作用时，即便常规控制流看上去好像它们总是都会发生，异常也可能使得它们中的某些不会发生。

这是一个非常糟糕的银行业代码。

```javascript
const accounts = {
  a: 100,
  b: 0,
  c: 20
};

function getAccount() {
  let accountName = prompt("Enter an account name");
  if (!accounts.hasOwnProperty(accountName)) {
    throw new Error(`No such account: ${accountName}`);
  }
  return accountName;
}

function transfer(from, amount) {
  if (accounts[from] < amount) return;
  accounts[from] -= amount;
  accounts[getAccount()] += amount;
}
```

`transfer`函数从一个给定账户转钱到另一个账户，其间询问转入账户的名字。如果给定一个不合法的账户名字，`getAcount`抛出一个异常。

但是`transfer`首先从转出账户扣除钱然后调用`getAcount`函数在它添加钱到另一个账户之前。如果在那个时间点被异常打断，就会让钱凭空消失了。

那段代码可以被写得更聪明些，比如在开始转钱之前调用`getAcount`。但是这样的问题常以更微妙的方式出现。即使函数看上去不会抛出异常，也可能会由于异常的情况或者包含程序员错误而抛出异常。

一种解决这个问题的方式是使用更少的副作用。再一次，一个编程风格，计算出新的值而不是修改存在的数据会有所帮助。如果一段代码停在了创造新值的过程中，没有人会看见半成品的值，并且这丝毫不是问题。

但是那不总是实用的。所以`try`语句还有一个额外的特性。它们可以后面跟着一个`finally`语句，可以与`catch`并存或者自己独立出现。一个`finally`块表明无论发生了什么，尝试去运行`try`块中的代码之后运行`finally`中的代码。
```javascript
function transfer(from, amount) {
  if (accounts[from] < amount) return;
  let progress = 0;
  try {
    accounts[from] -= amount;
    progress = 1;
    accounts[getAccount()] += amount;
    progress = 2;
  } finally {
    if (progress == 1) {
      accounts[from] += amount;
    }
  }
}
```
这个版本的函数追踪它的进度，并且如果离开的时候，它注意到在创造不一致的程序状态点中止，他会修复做过的破坏。

注意即使`finally`代码会在`try`块中抛出异常的时候运行，他也不干涉异常。在`finally`块运行之后，栈继续展开。

编写即使在异常出现在意料之外的地方可靠运行的程序是困难的。许多人根本不关心这个问题，并且因为异常通常是为特殊情况保留的，所以这个问题可能很少发生以至于从未被注意到。这是一件好事还是一件真正的坏事取决于当软件失败的时候会造成多大的破坏。

## 选择捕获

当异常一直到栈底还未被捕获时，将被环境所处理。这句话的含义随着环境的不同而不同。在浏览器中，一个错误描述被打印到JS控制台（可以通过开发者工具查看）。NodeJS，这个我们将在20章讨论的无浏览器环境，对数据差错更加小心。当一个未处理的异常发生的时候将会终止整个进程。

对于程序员的错误，让错误随风过去往往是你能做的最好的选择。一个未被处理的异常是一种标记一个被破坏程序的合理的方式，并且JS控制台将会提供问题发生时，哪些函数调用在栈上的信息。

对于常规的可能发生的问题，因为未处理的异常而崩溃是一个糟糕的策略。

非法使用语言，如引用一个不存在的绑定，在`null`上查找属性，或者调用非函数，将会导致抛出异常。这样的异常也可以被捕获。

当进入`catch`体的时候，我们所知道的是我们的`try`体中的某部分导致了异常。但是我们不知道是什么导致了这个异常。

JS（一个明显的遗漏）没有提供选择性地捕获异常的直接支持：要么你捕获它们所有，要么一个都不捕获。这使得在写`catch`块的时候很容易假设你得到的异常就是你想的那个异常。

但是可能不是这样。可能违反了其他一些假设，或者你可能引入了一个可能导致异常的bug。这有一个例子尝试去一直调用`promptDirection`直到得到一个合法的答案。

```javascript
for (;;) {
  try {
    let dir = promtDirection("Where?"); // ← typo!
    console.log("You chose ", dir);
    break;
  } catch (e) {
    console.log("Not a valid direction. Try again.");
  }
}
```

`for(;;)`结构是一种有意创造不会自终止循环的一种方式。只有在合法的方向给定的时候我们才会跳出循环。但是我们拼错了`promptDirection`，这将会导致一个"undefined variable"错误。因为`catch`块完全忽略异常值`e`，假定它知道问题是什么，错误地将绑定错误以为是坏的输入的错误。这不仅导致一个无限循环，还埋葬了关于错误拼写绑定的有用的错误信息。

一般规则是，不要做空白的捕获异常，除非是想要将异常路由到某个地方。例如，在网络中告诉另一个系统我们的程序崩溃了。即便这样也要仔细考虑如何隐藏信息。

所以我们想要捕获一种特定类型的异常。我们可以通过`catch`块来检查是否我们得到的异常是我们感兴趣的异常并且如果不是就重新抛出异常。但是我们怎么识别一个异常？

我们可以对比异常的`message`属性与我们期待的错误信息。但是那是一种不可靠的写代码的方式，我们将使用供人类使用的信息来做编程决定。一旦有人改变了message，那么代码将停止工作。

相反，让我们定义一个新的错误类型，并使用`instanceof`去识别它。

```javascript
class InputError extends Error {}

function promptDirection(question) {
  let result = prompt(question);
  if(result.toLowerCase() == "left") return "L";
  if(result.toLowerCase() == "right") return "R";
  throw new InputError("Invalid direction: " + result);
}
```

新的error类继承自`Error`。它没有定义自己的构造器，也就意味着它继承自`Error`的构造器，并接受一个字符串参数。事实上，它并没有定义任何东西，这个类是空的。`InputError`对象表现得类似`Error`对象，除了他们有不同的类名以便我们区分它们。

现在循环可以仔细地捕获这些异常。

```javascript
for (;;) {
  try {
    let dir = promptDirection("Where?");
    console.log("You chose ", dir);
    break;
  } catch (e) {
    if (e instanceof InputError) {
      console.log("Not a valid direction. Try again.");
    } else {
      throw e;
    }
  }
}
```

这只会捕获`InputError`的实例，并且忽略不相关的异常。如果重新引入了拼写错误，未定义异常将会恰当地报告。

## 断言

断言就是程序中检查某事是否表现得如它应该表现得一样的检查。它们不被用于正常操作可能出现的情况，而是去发现程序员的错误。

例如，如果`firstElement`作为一个不应该被在空数组上调用的函数，我们可以这样写：
```javascript
function firstElement(array) {
  if(array.length == 0) {
    throw new Error("firstElement called with []");
  }
  return array[0];
}
```

现在，代替悄悄地返回`undefined`（当获取一个不存在的数组属性时返回），而是会当你一旦错误使用的时候就大声爆破掉你的程序。这使得错误发生的时候不至于被忽视并且很容易找到原因。

我不推荐对每个可能的不好的输入写断言。那将是大量的工作并且可能产生非常讨厌的代码。你可以把它们保留给那些容易犯的错或者是那些你自己犯的错误。

## 总结

错误和不好的输入是生活的事实。编程中一个重要的方面就是发现，诊断和修复bug。如果你有自动化测试套件或者向程序中添加断言问题就会变得更容易去注意。

程序控制外的因素导致的问题通常应该被优雅地处理。有时，当问题可以在本地处理时，特殊的返回值是一种追踪它们的好方式。否则，异常可能更受偏爱。

抛出一个异常导致调用栈展开直到下一个封闭的`try/catch`块，或者直到堆栈的底部。异常值将会被给到捕获异常的`catch`块的参数，并且应该核实它是期望的异常种类然后做点什么。为了帮助处理由异常导致的不可预测的控制流，`finally`块可被用于确保当一个块完成时`finally`块中的代码总会运行。

## 练习

### RETRY

假设你有一个函数`primitiveMultiply`有20%的几率将两个数相乘，有80%的几率抛出`MultiplicatorUnitFailure`类型的异常。写一个函数包装这个笨重的函数并且让他持续尝试直到一次成功的调用，在那之后返回结果。

确保你只处理你尝试去解决的异常。
```javascript
class MultiplicatorUnitFailure extends Error {}

function primitiveMultiply(a, b) {
  if(Math.random() < 0.2) {
    return a * b;
  } else {
    throw new MultiplicatorUnitFailure("Klunk");
  }
}

function reliableMultiply(a, b) {
  // Your code here.
  for(;;) {
    try {
      return primitiveMultiply(a, b);
    } catch(e) {
      if(e instanceof MultiplicatorUnitFailure) {
        console.log("OOPS..failure");
      } else {
        throw e;
      }
    }
  }
}

console.log(reliableMultiply(8, 8));
// -> 64
```

### 锁着的盒子

考虑下面的（相当做作的）对象：
```javascript
const box = {
  locked: true,
  unlock() { this.locked = false; },
  lock() { this.locked = true;  },
  _content: [],
  get content() {
    if (this.locked) throw new Error("Locked!");
    return this._content;
  }
};
```

这是一个带锁的盒子。有一个数组在盒子中，但是只能在盒子未被锁的时候才能获取。直接获取私有的`_content`属性是被禁止的。

写一个叫做`withBoxUnlocked`的函数，接受一个函数值作为参数，解锁盒子，运行函数，并且确保函数返回前盒子被再次锁上，不考虑是否参数函数正常返回还是抛出一个异常。

```javascript
const box = {
  locked: true,
  unlock() { this.locked = false; },
  lock() { this.locked = true;  },
  _content: [],
  get content() {
    if (this.locked) throw new Error("Locked!");
    return this._content;
  }
};

function withBoxUnlocked(body) {
  // Your code here
  let locked = box.locked;
  if(!locked) return body();
  
  box.unlock();
  try{
    return body();
  } finally {
    box.lock();
  }
}

withBoxUnlocked(function() {
  box.content.push("gold piece");
});

try {
  withBoxUnlocked(function() {
    throw new Error("Pirates on the horizon! Abort!");
  });
} catch (e) {
  console.log("Error raised:", e);
}
console.log(box.locked);
// → true
```