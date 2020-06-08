---
title: EJ6
date: 2018-12-26 13:54:06
categories:
- Eloquent JavaScript
---

# 对象的秘密生活

第四章介绍了JS的对象。在编程文化中，我们有个叫做*object-oriented programming*的范式，也即是面向对象编程，就是将对象（和其相关概念）作为程序组织的中心原则。

即使没人同意它的定义，面向对象变成定形了许多编程语言的设计，包括JavaScript。这一章描述这些主意怎样被应用到JavaScript。

<!-- more -->

## 封装（Encapsulation）

面向对象编程的核心时将程序分成小的部分，而每一个小的部分只负责管理自己的状态。

用这种方式组织程序的话，可以保持很高的内聚性。负责程序其他部分的人不需要知道这部分程序的内部实现。每当这些local的细节变化的时候，只有附近的代码需要被修改。

以这种范式设计的程序的不同部分之间彼此的交互是通过接口(interface)的。接口就是以一种更加抽象的级别提供功能的函数或者变量的有限集，并隐藏具体功能的实现。

这样的程序片段用对象来建立模型。接口包含一个特定的方法和对象集合。作为接口的一部分的属性被称为是`public`的，而其他的，不应被外部代码看到的那些，被称为是`private`的。

许多语言提供了一种方式，用于区分共有和私有属性，并避免外部代码获取私有属性或者方法。JS，再一次实现了极简主义，没有这种方式，至少现在还没有。这项工作正在进行并即将添加到语言标准中。

虽然语言没有内建的区别。JS的编程人员也成功地运用这个主意。典型地，公共接口描述在文档或者注释中。也是很常见的，在属性名的开头放一个下划线(_)表明该属性是私有的。

将接口和实现分离是一个很棒的主意。通常被叫做封装(encapsulation)。

## 方法

方法只是保存函数值的一个属性。这是一个简单的方法：
```javascript
let rabbit = {};
rabbit.speak = function(line) {
    console.log(`The rabbit says '${line}'`);
};

rabbit.speak("I'm alive.");
// -> The rabbit says 'I'm alive.'
```

通常一个方法需要对调用它的对象进行一些什么操作。当一个函数作为方法调用的时候，一个叫做`this`的绑定在方法体中自动指向调用它的对象。
```javascript
function speak(line) {
    console.log(`The ${this.type} rabbit says '${line}'`);
}
let whiteRabbit = {type: "white", speak};
let hungryRabbit = {type: "hungry", speak};

whiteRabbit.speak("Oh my ears and whiskers, " + "how late it's getting!");
// → The white rabbit says 'Oh my ears and whiskers, how late it's getting!'
hungryRabbit.speak("I could use a carrot right now.");
// → The hungry rabbit says 'I could use a carrot right now.'
```
可以把`this`作为一个以不同的方式传进函数的额外的参数。如果想显式传递`this`，可以用函数的`call`方法，接收`this`值作为它的第一个参数，并将剩下的参数作为传递给函数的参数。
```javascript
speak.call(hungryRabbit, "Burp!");
// -> The hungry rabbit says 'Burp!'
```

因为每个函数都有自己的`this`绑定，这个绑定的值取决于函数调用的方式，不能够将`this`当作以`function`定义的普通函数的外部作用域（wrapping scope）。

*Since each function has its own this binding, whose value depends on the way it is called, you cannot refer to the this of the wrapping scope in a regular function defined with the function keyword*

箭头函数是不同的，他们不绑定他们自己的`this`，但是可以看见他们周围作用域的`this`绑定。因此，你可以做类似下面所做的事情，从一个本地函数中引用`this`。
```javascript
function normalize() {
    console.log(this.coords.map(n => n / this.length));
}
normalize.call({coords: [0, 2, 3], length: 5});
// -> [0, 0.4, 0.6]
```

如果map中的函数使用`function`关键字书写，那么上述代码将不会正常工作。

## 原型(prototypes)

```javascript
let empty = {};
console.log(empty.toString);
// -> function toString(){..}
console.log(empty.toString());
// -> [object Object]
```
我获取了一个空对象中的属性!

实际并不是这样，我保留了关于JS对象工作机制的一些信息。除了他们的属性集合，大多数的对象也有一个`prototype`。原型就是另外一个对象，被用于作为属性的回退source。当一个对象被请求一个他没有的属性，原型就会根据那个属性被搜索，然后是原型的原型等等。类似于JAVA中的类的继承，所有的类都继承自Object类，而继承链上还有可能有其他类。

所以空对象的原型是谁呢？它就是伟大的祖先原型，在所有对象后面的实体：`Object.prototype`。
```javascript
console.log(Object.getPrototypeOf({}) == Object.prototype);
// -> true
console.log(Object.getPrototypeOf(Object.prototype));
// -> null
```
`Object.getPrototypeOf`返回一个对象的原型。

JS对象的原型关系形成了一个树状结构，这个结构的根部是`Object.prototype`。它为所有对象提供了一些方法，如`toString`方法，将一个对象转换为字符串表示。

许多对象并不是直接把`Object.prototype`作为他们的原型，取而代之的是，还有一个对象提供一个不同的默认属性集。函数源自于`Function.prototype`,数组源自于`Array.prototype`。

```javascript
console.log(Object.getPrototypeOf(Math.max) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf([]) ==
            Array.prototype);
// → true
```

这种原型对象本身有一个原型，通常是`Object.prototype`，所以仍然间接提供了类似`toString`这样的方法。

可以使用`Object.create`去创造一个具有特定原型的对象。

```javascript
let protoRabbit = {
  speak(line) {
    console.log(`The ${this.type} rabbit says '${line}'`);
  }
};
let killerRabbit = Object.create(protoRabbit);
killerRabbit.type = "killer";
killerRabbit.speak("SKREEEE!");
// → The killer rabbit says 'SKREEEE!'
```
在对象表达式中的`speak(line)`是一种定义方法的简写形式。它创建一个属性名叫做`speak`并且指定这个函数作为它的值。

这个“原型”兔子作为一个为所有兔子共享属性的容器。一个独立的兔子对象，如killer兔子，包含它自身的苏偶偶属性，如`type`属性，以及从它的原型上继承的共享属性。

## 类

JS的原型系统可以被不正式地理解为面向对象中类的概念。一个类定义一种类型对象的形状-也就是拥有什么方法和属性。这样的对象叫做这个类(class)的实例(Instance)。

原型在定义所有实例共享的属性时，非常有用，比如方法。那些不同实例特有的属性，需要直接定义在对象本身。

所以为了创造一个给定类的实例，必须创造一个对象，这个对象衍生自合适的原型，但是还要确保这个原型有着这个类的实例应该拥有的属性。这就是构造器(constructor)函数所做的事情。

```javascript
function makeRabbit(type) {
    let rabbit = Object.create(protoRabbit);
    rabbit.type = type;
    return rabbit;
}
```

JS提供了一种定义这种类型函数的更简单的方式。如果你在函数调用前放一个`new`，这个函数就会被当作构造器。这意味着一个拥有正确原型的对象会自动被创建，并绑定到函数中的`this`，在函数结尾返回。

*The prototype object used when constructing objects is found by taking the prototype property of the constructor function.*

构造对象时使用的原型对象是通过构造函数的prototype属性找到的。

```javascript
function Rabbit(type) {
  this.type = type;
}
Rabbit.prototype.speak = function(line) {
  console.log(`The ${this.type} rabbit says '${line}'`);
};

let weirdRabbit = new Rabbit("weird");
```

构造器函数（事实上所有的函数）自动获得一个名为`prototype`的属性，默认保存着一个空的源自于`Object.prototype`的对象。如果你愿意的话可以用新的对象重写它。或者可以向已经存在的对象上添加新的属性，如例子中的那样。

约定俗成地，构造器的名字首字母大写，以便可以与其他函数区分开来。

理解prototype关联到构造器函数（通过prototype属性）的方式和对象有一个prototype（可以通过Object.getPrototypeOf得到）的区别是很重要的。实际的函数的prototype是`Function.prototype`，因为构造器是函数。它的`prototype`属性保存着原型，用于通过它创造实例的时候使用。
```javascript
console.log(Object.getPrototypeOf(Rabbit) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf(weirdRabbit) ==
            Rabbit.prototype);
// → true
```

## 类标记

所以JS的类就是带有一个prototype属性的构造器函数。直到2015年，都是必须这样书写的。近来，我们有了一个不是那么尴尬的写法。

```javascript
class Rabbit {
    constructor(type) {
        this.type = type;
    }
    speak(line) {
        console.log(`This ${this.type} rabbit says '${line}'`);
    }
}

let killerRabbit = new Rabbit("killer");
let blackRabbit = new Rabbit("black");
```
`class`关键字开始一个类声明，允许我们在一个地方定义一个构造器和一组方法。任何数量的方法可以写在声明大括号的里面。名为`constructor`的方法被特殊处理。
它提供了实际的构造器函数，将会绑定到`Rabbit`名下。而其他函数会被打包到构造器的`prototype`属性上。因此，上面的类声明等同于我们之前的构造器定义。只是看上去更漂亮。

类声明现在只允许方法，也就是保存着函数值的属性被添加到函数的`prototype`属性上去。如果想保存一个非函数值上去可能有点不太方便。下一个版本的JS可能会改进这个，现在只需要在定义类之后直接修改`prototype`即可。

类似`function`,`class`也可以被用于语句和表达式中。当被用在表达式中，没有定义一个绑定而只是提供作为一个值的构造器。可以在类表达式中省略类名。
```javascript
let object = new class { getWord() { return "hello"; } };
console.log(object.getWord());
// → hello
```

## 覆盖派生的属性

当为对象添加一个属性的时候，不管这个属性在不在原型中，都会被添加到对象本身。如果原型也有一个同名属性，那么原型上的同名属性将不再有效，将被新添加的属性覆盖。

```javascript
Rabbit.prototype.teeth = "small";
console.log(killerRabbit.teeth);
// → small
killerRabbit.teeth = "long, sharp, and bloody";
console.log(killerRabbit.teeth);
// → long, sharp, and bloody
console.log(blackRabbit.teeth);
// → small
console.log(Rabbit.prototype.teeth);
// → small
```

下面的图片描绘了代码运行过后的情形。`Rabbit`和`Object`原型在killerRabbit后面作为一种背景，对象本身找不到的属性会被在这些原型中查找。

{% asset_img rabbit.png %}

重写存在原型中的属性是个有用的事情。对于实例中出现的不寻常的对象的某个特定值，可以通过重写去隐藏掉原型上的值，而保持其他寻常的对象的值不变。

重写也用于给一个函数和数组原型不同的`toString`方法，而不是基本的对象原型。
```javascript
console.log(Array.prototype.toString == Object.prototype.toString);
// -> false
console.log([1,2].toString());
// -> 1, 2
```
在一个数组上调用`toString`方法类似在数组上调用`join(',')`。将逗号作为分隔符置于数组值中间。直接调用`Object.prototype.toString`会产生一个不同的字符串。那个方法不知道数组，所以就在中括号之间生成一个`object`单词和对应的类型名。
```javascript
console.log(Object.prototype.toString.call([1,2]));
// -> [object Array]
```

## maps

我们之前看过`map`，通过对数据结构中的元素应用一个函数进行转换。令人困惑的是，同样的单词被用于描述一个相关但是不同的事情。

map是一个数据结构，关联键值对。例如，想要映射名字到年龄。我们可以使用对象。

```javascript
let ages = {
  Boris: 39,
  Liang: 22,
  Júlia: 62
};

console.log(`Júlia is ${ages["Júlia"]}`);
// → Júlia is 62
console.log("Is Jack's age known?", "Jack" in ages);
// → Is Jack's age known? false
console.log("Is toString's age known?", "toString" in ages);
// → Is toString's age known? true
```

对象的属性名是人的名字，对应的值是年龄。但是我们并没有一个叫做toString的人，用in操作符得到的结果却是相反的。这是因为，对象派生自`Object.prototype`，看起来好像拥有这个属性。

使用普通对象作为map是危险的。有几种可能的方式避免这个问题。首先，是可以创造没有原型的对象的。如果传递`null`给`Object.create`，结果的对象将不会派生自`Object.prototype`，那么就可以安全的作为map使用。
```javascript
console.log("toString" in Object.create(null));
// -> false
```
对象的属性名必须是字符串。如果需要一个map，并且key不能够很容易的转换为字符串，如对象，就不能用对象实现一个map。

幸运的是，JS有一个叫做Map的类就是用于创建map的。它存储映射并允许任何类型的键。
```javascript
let ages = new Map();
ages.set("Boris", 39);
ages.set("Liang", 22);
ages.set("Júlia", 62);

console.log(`Júlia is ${ages.get("Júlia")}`);
// → Júlia is 62
console.log("Is Jack's age known?", ages.has("Jack"));
// → Is Jack's age known? false
console.log(ages.has("toString"));
// → false
```

set,get,has方法是Map对象接口的一部分。书写一个快速update和search的数据结构不是很容易地，但是有别人已经为我们写好了，并且我们可以使用这个接口使用他们的成果。

如果你确实想用一个对象实现map，知道`Object.keys()`只返回对象自己的键而不返回原型中的键是有用的。作为in操作符的替代，`hasOwnProperty`只对对象本身的属性返回true。
```javascript
console.log({x: 1}.hasOwnProperty("x"));
// → true
console.log({x: 1}.hasOwnProperty("toString"));
// → false
```

## 多态

当在对象上调用`String`函数的时候（将一个值转换为字符串），将会调用对象的`toString`方法去计算一个有意义的字符串表示这个对象。有一些原型定义了自己的`toString`方法，所以会返回比`[object Object]`更有意义的字符串。你也可以自己实现这种功能。
```javascript
Rabbit.prototype.toString = function() {
  return `a ${this.type} rabbit`;
};

console.log(String(blackRabbit));
// → a black rabbit
```

这是一个伟大的想法的简单实例。当一段代码要与有特定接口（toString）的对象做交互时，任何种类的支持这种接口的对象都可以插进这段代码，并且它会正常运行。

这种技巧叫做多态（polymorphism)。多态代码可以各种不同类型的值，只要他们支持期望的接口。

第四章提到的`for/of`循环可以遍历几种数据结构。这是另一种多态的例子。这样的循环期望数据结构暴露一个特定的接口，数组和字符串就是这样。我们也可以在我们自己的对象中添加这个接口！但是做这个之前，先要知道symbol是什么。

## symbols

多个接口使用相同的名字做不同的事情是可能存在的。比如，我可以定义一个接口，接口中toString方法应该转换对象到一团纱线。对于对象不可能同时支持这个接口和toString的标准使用。

JS提供了一种解决方案。当物品说属性名是字符串时，说法并不准确。通常情况下是这样但是他们也可以是symbols。symbol是用`Symbol`函数创造的值。不像字符串，新创建的symbol是独一无二的，你不能创造同一个symbol两次。

```javascript
let sym = Symbol("name");
console.log(sym == Symbol("name"));
// → false
Rabbit.prototype[sym] = 55;
console.log(blackRabbit[sym]);
// → 55
```

传递给`Symbol`的参数会在你将symbol转换为字符串的时候显示出来，并且使得识别一个symbol变得容易（比如在控制台打印的时候）。但是除了那个没有其他的意义了-多个symbol也可能有相同的值。

独一无二并且可用于属性名使得symbol很适合定义可以与其他属性和谐共处的接口，而且无论他们的名字是什么。
```javascript
const toStringSymbol = Symbol("toString");
Array.prototype[toStringSymbol] = function() {
  return `${this.length} cm of blue yarn`;
};

console.log([1, 2].toString());
// → 1,2
console.log([1, 2][toStringSymbol]());
// → 2 cm of blue yarn
```

可以在对象表达式和类中通过在属性名外面包裹中括号包含symbol属性。这使得属性名将会被求值，有点像对象的方括号获取属性标记，允许我们参考保存symbol的变量。
```javascript
let stringObject = {
  [toStringSymbol]() { return "a jute rope"; }
};
console.log(stringObject[toStringSymbol]());
// → a jute rope
```

## 迭代器接口

`for/of`循环的参数对象被期望是`iterable`。这意味着它有一个名为`Symbol.iterator`（是一个语言定义的symbol，存储在Symbol中）的方法。

当被调用的时候，方法应该返回一个提供第二个接口的对象，也就是`iterator`。这就是它迭代的实际的东西。有一个`next`方法返回下一个结果。结果应该是一个拥有`value`属性和`done`属性的对象。value表示下一个值，而done表示是否遍历完成。

`next`,`value`,`done`属性名都是普通字符串而不是symbols。只有`Symbol.iterator`，可能被添加一些不同的对象，是一个实际的symbol。

我们可以直接使用这个接口。
```javascript
// okIterator是对字符串"OK"迭代的迭代器
let okIterator = "OK"[Symbol.iterator]();
console.log(okIterator.next());
// → {value: "O", done: false}
console.log(okIterator.next());
// → {value: "K", done: false}
console.log(okIterator.next());
// → {value: undefined, done: true}
```

让我们实现一个iterable的数据结构。我们将构建一个矩阵类。

```javascript
class Matrix {
    constructor(width, height, element = (x, y) => undefined) {
        this.width = width;
        this.height = height;
        this.content = [];

        for(let y = 0; y < height; y++) {
            for(let x = 0; x < width; x++) {
                this.content[y * width + x] = element(x, y);
            }
        }
    }

    get(x, y) {
        return this.content[y * this.width + x];
    }

    set(x, y, value) {
        this.content[y * this.width + x] = value;
    }
}
```
这个矩阵类将内容存放在一个`width * height`个元素的一维数组中。元素一行挨一行的被存储并用基于0的索引。如第五行的第三个元素的索引是`4 * width + 2`。

构造器方法接受width，height以及一个可选的用域填充矩阵初始值的函数。同时还包含get和set方法，分别用于检索和改变矩阵中的值。

当遍历一个矩阵的时候，除了对元素本身感兴趣还对元素的位置感兴趣。所以我们的迭代器将会产生一个对象，并拥有x，y和value属性。

```javascript
class MatrixIterator {
    constructor(matrix) {
        this.x = 0;
        this.y = 0;
        this.matrix = matrix;
    }

    next() {
        if(this.y == this.matrix.height) return {done: true};

        let value = {x: this.x, y:this.y, value: this.matrix.get(this.x, this.y)};
        this.x++;
        if(this.x == this.matrix.width) {
            this.x = 0;
            this.y++;
        }
        return {value, done: false};
    }
}
```
这个类用x和y属性追踪迭代矩阵的进度。`next`方法首先见擦汗矩阵的底部是否被到达。如果尚未到达，创造一个对象，用来保存当前值并且随后改变位置，如果有必要的话移动到下一行。

我们通过以下代码让Matrix类可迭代。
```javascript
Matrix.prototype[Symbol.iterator] = function() {
    return new MatrixIterator(this);
}
```
我们现在就可以用`for/of`迭代矩阵了。
```javascript
let matrix = new Matrix(2, 2, (x, y) => `value ${x},${y}`);
for(let {x, y, value} of matrix) {
    console.log(x, y, value);
}
// → 0 0 value 0,0
// → 1 0 value 1,0
// → 0 1 value 0,1
// → 1 1 value 1,1
```

## getter, setter, static

接口通常包含方法，但是包含保存非函数值的属性也是可以的。例如，Map对象有一个size属性告诉你map中有多少键。

没必要在实例的属性中直接计算和存储这样一个属性。即便是直接获取的属性背后也可能隐藏着一个方法调用。这样的方法叫做`getters`，并且他们通过在对象表达式或者类声明中在方法名前面加上`get`定义。
```javascript
let varyingSize = {
  get size() {
    return Math.floor(Math.random() * 100);
  }
};

console.log(varyingSize.size);
// → 73
console.log(varyingSize.size);
// → 49
```

每当有人读取这个对象的size属性时，关联的方法就被调用。可以在写属性的时候利用一个`setter`。
```javascript
class Temperature {
  constructor(celsius) {
    this.celsius = celsius;
  }
  get fahrenheit() {
    return this.celsius * 1.8 + 32;
  }
  set fahrenheit(value) {
    this.celsius = (value - 32) / 1.8;
  }

  static fromFahrenheit(value) {
    return new Temperature((value - 32) / 1.8);
  }
}

let temp = new Temperature(22);
console.log(temp.fahrenheit);
// → 71.6
temp.fahrenheit = 86;
console.log(temp.celsius);
// → 30
```
`Temperature`类只允许你读取或者写温度，要么以摄氏度的形式或者华氏度的形式。但是内部只存储了摄氏度，只是在`fahrenheit`的getter和setter中自动转换。

有时想要直接关联一些属性到构造器函数，而不是原型。这样的方法不能直接被实例访问，但是可以被用于提供一种额外的方式去创造实例。

在类声明内部，以static开头的方法存储在构造器中，所以`Temperature`类允许`Temperature.fromFahrenheit(100)`用华氏度去创造一个`Temperature`实例。

## 继承

一些矩阵是对称的，也就是坐标x,y和坐标y,x的值保持相同。

设想我们需要一个类似`Matrix`的数据结构但是是对称结构的。我们可以从头写一个，但会有大量重复代码。

JS的原型系统允许我们创造一个类似于旧类的新类，同时重新对属性定义。新类的原型派生自旧的原型，但是为set方法添加了一个新的定义。

在面向对象的编程术语中，这叫做继承(inheritance)。新的类从旧类继承属性和行为。

```javascript
class SymmetricMatrix extends Matrix {
  constructor(size, element = (x, y) => undefined) {
    super(size, size, (x, y) => {
      if (x < y) return element(y, x);
      else return element(x, y);
    });
  }

  set(x, y, value) {
    super.set(x, y, value);
    if (x != y) {
      super.set(y, x, value);
    }
  }
}

let matrix = new SymmetricMatrix(5, (x, y) => `${x},${y}`);
console.log(matrix.get(2, 3));
// → 3,2
```

`extends`关键字的使用表明这个类不应该基于默认的`Object`原型而是其他类。这个被基于的类叫做superclass(父类),派生出来的类叫做subclass(子类)。

为了初始化一个`SymmetricMatrix`实例，构造器需要通过`super`关键字调用父类的构造器。这是必要的因为新类需要表现得和父类一致。为了确保矩阵对称，对对角线以下的坐标进行交换。

set方法再一次用了super关键字，不过这一次是调用父类中特定的方法。super提供了一种调用父类方法的能力。

继承允许我们费更少的劲去构建稍微不同于已有的数据类型的数据类型。它也是面向对象传统的一部分，其他两者是封装和多态，后两个是精彩的主意，但是继承存在争议。

继承将类缠结在一起，增加了程序的耦合度，而封装和多态可以被用于彼此分离的代码。当要继承一个类时，必须要知道这个类是怎样工作的。继承是一个有用的工具，但是不应该是你寻求的第一手工具。

## instanceof操作符

有时知道是否一个对象派生自一个特定的类是有用的，JS提供了一个叫做`instanceof`的二元操作符。

```javascript
console.log(
  new SymmetricMatrix(2) instanceof SymmetricMatrix);
// → true
console.log(new SymmetricMatrix(2) instanceof Matrix);
// → true
console.log(new Matrix(2, 2) instanceof SymmetricMatrix);
// → false
console.log([1] instanceof Array);
// → true
```

这个操作符可以看见继承的类型，所以`SymmetricMatrix`也是`Matrix`的一个实例。操作符也可被用于标准的如`Array`这样的构造器。差不多所有的对象都是`Object`的实例。

## 总结

对象不仅仅只保存自己的属性，他们有一个原型，是其他的对象。他们表现的就好像拥有自己没有的属性只要他们的原型拥有这个属性。普通对象的原型都是`Object.prototype`。

构造器函数名字以大写字母开头，可用`new`操作符创造新对象。新对象的原型是构造器的`prototype`属性。通过这个特性可以将所有实例共享的属性放到原型中。同时也有一个`class`标记提供了一种更清晰的方式去定义构造器和构造器的原型。

可以定义getters和setters，每当对象的属性被获取的时候调用对应的方法。静态方法是直接存储到构造器中的方法，而不是原型。

`instanceof`操作符可以通过给定一个对象和一个构造器，告诉你是否这个对象是这个构造器的一个实例。

关于对象一个很有用的事是为他们指定一个接口，并且所有人应该只通过这个接口和你的对象交互。其余的组成对象的细节被封装起来，隐藏在接口后面。

不止一种类型可以实现同一个接口。使用接口的代码自动知道如何与提供这个接口的不同对象交互。这叫做多态。

当实现只是在某些细节方面不同的类，将新的类作为旧类的字类是很有用的，继承了父类的部分行为。

## 练习

### 一个vector类型

写一个`Vec`类代表一个二维空间的矢量。接受x和y参数（数字），应该保存到同名的属性中。

给`Vec`原型两个方法，`plus``minus`方法，接受一个不同的矢量对象，返回一个新的vector。

为原型添加一个getter属性`length`来计算矢量的长度（到0,0的距离）

```javascript
// Your code here.
class Vec {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    plus(vector) {
        return new Vec(this.x + vector.x, this.y + vector.y);
    }

    minus(vector) {
        return new Vec(this.x - vector.x, this.y - vector.y);
    }

    get length() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }
}

console.log(new Vec(1, 2).plus(new Vec(2, 3)));
// → Vec{x: 3, y: 5}
console.log(new Vec(1, 2).minus(new Vec(2, 3)));
// → Vec{x: -1, y: -1}
console.log(new Vec(3, 4).length);
// → 5
```

### groups

标准JS环境提供了一个叫做set的数据结构。其实就是个不允许有重复元素的集合。

写一个名为`Group`的类。类似于`Set`，它有`add`,`delete`,`has`方法。构造器创造一个空的group，`add`方法添加一个值到group中（如果存在）。`delete`从group中删除一个元素，`has`返回一个布尔值表明是否参数是group中的一员。

使用`===`操作符或者`indexOf`确定是否两个值一样。

给定一个静态的`from`方法，接受一个可迭代的对象，通过迭代它所有的值创造一个group。

```javascript
class Group {
  // Your code here.
  constructor() {
      this.content = [];
  }

  add(element) {
      if(!this.has(element)) this.content.push(element);   
  }

  delete(element) {
      if(this.has(element)) {
          this.content = this.content.filter(x => x !== element);
      }
  }

  has(element) {
  	return this.content.includes(element);
  }

  static from(iterable) {
      let g = new Group();
      for(let element of iterable) {
         g.add(element);
      }
      return g;  
  }
}

let group = Group.from([10, 20]);
console.log(group.has(10));
// → true
console.log(group.has(30));
// → false
group.add(10);
group.delete(10);
console.log(group.has(10));
// → false
```

### 可迭代的groups

在前面的部分参考`iterator`接口的使用方法，使得`Group`类可迭代。

如果你用数组代表一个group的成员，不要返回一个调用数组的`Symbol.iterator`方法返回的迭代器。那不是这个练习的目的。

如果group在迭代过程中被改变你的迭代器会表现得异常是OK的。
```javascript
// Your code here (and the code from the previous exercise)
class Group {
  // Your code here.
  constructor() {
      this.content = [];
  }

  add(element) {
      if(!this.has(element)) this.content.push(element);   
  }

  delete(element) {
      if(this.has(element)) {
          this.content = this.content.filter(x => x !== element);
      }
  }

  has(element) {
  	return this.content.includes(element);
  }

  static from(iterable) {
      let g = new Group();
      for(let element of iterable) {
         g.add(element);
      }
      return g;  
  }
  [Symbol.iterator]() {
  	return new GroupIterator(this);
  }
}

class GroupIterator {
    constructor(group) {
        this.position = 0;
        this.group = group;
    }

    next() {
        if(this.position >= this.group.content.length) return {done: true};

        let value = this.group.content[this.position];
        this.position++;
        return {value, done: false};
    }
}
for (let value of Group.from(["a", "b", "c"])) {
  console.log(value);
}
// → a
// → b
// → c
```

### 借用一个方法

早先提到过，`hasOwnProperty`可作为更加健壮的`in`操作符的替代，如果想忽略原型上的属性时。但是如果你的map需要包含`hasOwnProperty`键呢？这个属性就会遮蔽掉这个方法值。想一个办法解决这个问题。

```javascript
let map = {one: true, two: true, hasOwnProperty: true};

// Fix this call
console.log(map.hasOwnProperty("one"));
// → true
```

```javascript
console.log(Object.prototype.hasOwnProperty.call(map, "one"));
```
`hasOwnProperty`方法来自于`Object.property`，而
函数的call方法可以指定函数调用的`this`绑定。