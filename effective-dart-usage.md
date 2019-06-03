# effective-dart-usage

本章是**effective dart**系列中最使用的指南。您将直接在您的Dart代码中应用这些指导原则。
<a name="top"></a>
## 🔥 Effective Dart: Usage 🔥

- 库
  - [DO] [`part of`指令之后使用字符串](#1-1)
  - [DON'T] [不要在你的库的src目录下引入其他库](#1-2)
  - [PREFER] [库lib目录下的引入请使用相对路径](#1-3)
- 字符串
  - [DO] [使用`adjacent strings`串联字符串而不是使用`+`号](#2-1)
  - [PREFER] [使用模板字符串来拼接值和字符而不是用`+`拼接](#2-2)
  - [AVOID]
    [在不需要使用大括号时省略大括号](#2-3)
- 集合
  - [DO] [尽量使用字面量定义集合](#3-1)
  - [DON'T] [不要使用`.length`去判断集合是否为空](#3-2)
  - [CONSIDER] [使用高阶函数来转换序列](#3-3)
  - [AVOID] [避免在`Iterable.forEach()`里写函数](#3-4)
  - [DON'T] [不要使用`List.from()`除非你想转换集合类型](#3-5)
  - [DO] [使用`whereType()`去过滤集合类型](#3-6)
  - [DON'T] [当相邻操作符支持转换类型时不要使用`cast()`](#3-7)
  - [AVOID] [避免使用`cast()`](#3-8)
- 函数
  - [DO] [使用函数声明为函数赋名](#4-1)
  - [DON'T] [在tear-off函数符合条件的情况下，不要创建lambda函数](#4-2)
- 参数
  - [DO] [使用`=`为命名参数设置默认值](#5-1)
  - [DON'T] [不要将默认值显式设置为`null`](#5-2)
- 变量
  - [DON'T] [不要将初始化变量显式设置为`null`](#6-1)
  - [AVOID] [避免存储你可以计算的值](#6-2)
- 成员
  - [DON'T] [不要在不必要的时候设置`getter`和`setter`](#7-1)
  - [PREFER] [只读属性使用`final`声明](#7-2)
  - [CONSIDER] [对于一个简单属性的获取使用`=>`](#7-3)
  - [DON'T] [不要在不必要的时候使用`this`](#7-4)
  - [DO] [尽量在成员声明时赋值初始值](#7-5)
- 构造函数
  - [DO] [尽量使用构造函数形式初始化字段](#8-1)
  - [DON'T] [不要为`initializing formals`函数参数声明类型](#8-2)
  - [DO] [构造函数body为空时使用`;`而不是`{}`](#8-3)
  - [DON'T] [不要使用`new`关键字](#8-4)
  - [DON'T] [不要重复声明`const`](#8-5)
- 错误处理
  - [AVOID] [避免没有使用on捕获错误](#9-1)
  - [DON'T] [在没有on语句处理异常时，不要直接丢弃错误误](#9-2)
  - [DO] [在程序错误的情况下抛出`Error`异常](#9-3)
  - [DON'T] [不要处理Error类型的异常](#9-4)
  - [DO] [使用`rethrow`关键词重新抛出无法处理的异常](#9-5)
- 异步
  - [PREFER] [使用`async/await`优于传统的`Future`](#10-1)
  - [DON'T] [不要在`async`没有任何作用时使用它](#10-2)
  - [CONSIDER] [使用高阶函数处理转换流`stream`](#10-3)
  - [AVOID] [避免直接使用`Completer`类](#10-4)
  - [DO] [当参数声明类型为`FutureOr<T>`时候，参数可能为`Object`的情况下请用`Future<T>`做类型判断](#10-5)

### 库

> 以下的建议可以帮助你以一致性、可维护的方式在多个文件中编写程序。。为了让这些指导方针的简短，以下的“import”涵盖了导入和导出指令，因为这些准则两者皆适用。



<a name="1-1"></a>
#### [DO] `part of`指令之后使用字符串

> 许多dart开发者会避免使用`part`，因为当每个library都是单文件时很容易读懂整个代码。如果你选择使用`part`来拆分你的库文件，Dart要求其他文件需要使用`part of`来显式声明所属库。因为遗留原因Dart允许`part of`参数为库名，这让工具很难识别库的主文件，并且容易产生歧义。
> 更建议的是使用URI字符串的方式声明库主文件，就像你在其他诸如`import`指令一样，下面是一个例子：


假设你有一个 my_library库

```dart
library my_library;
    part "some/other/file.dart";
```

part file的声明应该是

```
// good
    part of "../../my_library.dart";
```

而不是

```
//bad
    part of my_library;
```


<a name="1-2"></a>
#### [DON'T] 不要引入第三方库中`src`目录下的文件

> `lib`目录下的`src`目录所包含的源代码对于库来说是私有实现，因此lib的维护者在对库进行版本升级时也会考虑这种约定，所以私有实现可以随意更改而不会对包产生破坏性更新。


> 这意味着如果你引入了其他包的私有库/文件，非破坏性的更新也会破坏你的代码。



<a name="1-3"></a>
#### [PREFER] 同个库lib目录下的引入请使用相对路径

```markdown
my_package
        └─ lib
            ├─src
            |   └─ utils.dart
            └─api.dart
```

如果api.dart想要导入utils.dart，那么应该这么做：

```dart
// good
    import 'src/utils.dart';
```

而不是

```
// bad
    import 'package:my_package/src/utils.dart'
```

其实并没有很特别的理由选择前者，主要是前者描述短一点并且我们希望保持一致

另外“在您自己的包的lib路径中”部分很重要。lib路径内的代码可以导入其他库中lib内部的library。位于lib路径外的library可以可以使用相对路径导入其他库中位于lib路径外的library。

但是不应该跨lib访问。位于lib路径外部的library不应该使用相对路径来访问lib路径下的库，反之亦然。因为这样做将影响Dart识别两个URIS是否引用为同一个库的能力。因此请遵循以下两条规则:

- 导入路径永远不应该包含/lib/。
- lib下的库不应该使用../以转义lib目录。

**[⬆ back to top](#top)**

<a name="cc4dd1da"></a>
## 字符串

> 以下是一些Dart语言中处理字符串的最佳实践



<a name="2-1"></a>
#### [DO] 使用`adjacent strings`串联字符串而不是使用`+`号

Dart中你可以使用如下的方式(相邻字符串)串联字符串，这种方式可以很容易将一个超长字符串分割多行而且无需使用`+`操作符

```dart
// good
    show(
        'what happend in the dartlang world'
        'and what can we do now ?');
```

```dart
// bad
    show('what happend in the dartlang world'+
        'and what can we do now ?');
```


<a name="2-2"></a>
#### [PREFER] 使用模板字符串来拼接变量值和字符

如果你有其他语言的开发经验，那么你可能已经习惯使用+来拼接构建由字符和其他变量值组成的字符串。这在Dart中确实可行，但是使用插值几乎总是更简洁、更短:

```dart
// good
    'Hello , $name ! you are ${year - birth} years old';
```

```
// bad
    'Hello ,'+name+' you are '+(year - birth).toString()+' years old';
```


<a name="2-3"></a>
#### [AVOID] 在无需使用花括号时省略它

如果只是插入一个简单的标识符，而不是紧跟着更多的表达式，则应该省略{}。

```dart
// goods
    'Hi , $name'
    'Wear your wildest $decade's outfit'

    // bad
    'Hi, ${name}'
    "Wear your wildest ${decade}'s outfit"
```

**[⬆ back to top](#top)**

### 集合

Dart支持4种开箱即用的集合类型：lists,maps,queues 以及 sets,下面是有关集合的一些最佳实践。


<a name="3-1"></a>
#### [DO] 尽量使用字面量(literal syntax)定义集合

有两种方式定义一个空数组：`[]`和`List()`，同样的有三种方式定义Linked HashMap:`{}`,`Map()`和`LinkedHashMap()`

如果你想生成固定长度集合或者其他的自定义类型集合时请使用构造器，其他情况使用字面量语法。

```dart
// good
    var points = [];
    var addresses = {};
```

```
// bad
    var points = List();
    var addresses = Map();
```

> 必要时你可以使用类型参数指明集合内的类型


```dart
// good
    var points = <Point>[];
    var addresses = <String, Addresses>{};
```

```
// bad
    var points = List<Point>();
    var addresses = Map<String, Addresses>();
```

> 注意这些建议不适用于这些类的命名构造函数。`List.from()`，`Map.fromIterable()`等方法有各自的用途。比如你想创建一个定长的集合，那么此时可以使用`List()`构造函。



<a name="3-2"></a>
#### [DON'T] 不要使用.length去判断集合是否为空

Iterable 并不要求集合类能够知道自身的长度，也不要求 .lenght 在常量的时间内返回结果。因为如果只是希望判断一个集合类为空时使用.length，获取length的内部计算的过程可能很慢。

相比于使用`.length`去判定一个集合是否为空，更建议使用阅读性更强、性能更优的`.isEmpty`和`.isNotEmpty`。

```dart
// good 
    if ( list.isEmpty ) return 'this is a empty list';
    if ( array.isNotEmpty ) return 'wooo, a non-empty array';

    // bad
    if( list.length == 0 ) return 'this is a empty list';
    if( !array.isEmpty ) return 'wooo, a non-empty array';
```


<a name="3-3"></a>
#### [CONSIDER] 使用高级函数来转换序列

如果您有一个集合，并希望根据某些条件转换生成一个新的集合，那么使用.map()、.where()以及**Iterable**上的其他方法后代码量会更短并且更具有声明性。

使用这些而不是**for循环**可以清楚地表达你的代码意图是生成一个新的序列，并且不会对原集合产生副作用。

```dart
// good
    var coolBoy = Boys
        .where((boy) => boy.isRich)
        .where((boy) => boy.isTall)
        .map((boy) => boy.name);
```


<a name="3-4"></a>
#### [AVOID] 避免直接在Iterable.forEach()内直接使用函数

`forEach()`在JavaScript中被广泛使用，因为内建的for-in循环的行为有时无法达到预期。但是在Dart中，如果您想遍历一个序列，惯用的方法是使用循环。

```dart
// good
    for ( var i in people ) {
        // your function here
    }

    // bad
    people.forEach((i) {
        // your function here
    });
```

有一种情况例外，当我们的处理函数已存在（无需再次申明），并可以接受元素作为参数

```dart
// good
    people.forEach(print);
```


<a name="3-5"></a>
#### [DON'T] 不要使用List.from()除非你想转换集合的类型

对于给定的`Iterable`对象,这里有两种方式生成新的`List`(包含一样的子元素)

```dart
var copy1 = iterable.toList();
    var copy2 = List.from(iterable);
```

这两种方式明显的区别是第一种方式简短一点，另外一个重要的区别是:第一种会保留集合的类型参数

```dart
// good

    // Creates a List<int>
    var iterable = [1,2,3]
    // Prints "List<int>"
    print(iterable.toList().runtimeType);
```

```
//bad
    
    // Creates a List<int>
    var iterable = [1, 2, 3];
    // Prints "List<dynamic>":
    print(List.from(iterable).runtimeType);
```

如果你想改变集合的泛型类型，那么可以使用List.from()

```dart
var numbers = [1, 2.3, 4]; // List<num>.
    numbers.removeAt(1); // Now it only contains integers.
    var ints = List<int>.from(numbers); // List<int>
```

但是，如果你仅仅是向copy原序列，并保持它原有的类型，或者并不关心序列内的类型，那么建议使用 `toList`

<a name="3-6"></a>
#### [DO]使用whereType()过滤集合类型

假设你的集合中包含多种类型，并且你只想获取int类型,你当然可以使用`.where()`操作

```dart
// bad
    var objs = [1, '2', 3, '4'];
    var ints = objects.where((e) => e is int);
```

这样的代码很冗长，更严重的返回的类型可能并不是你想要的。比如在例子中，where操作后会返回`Iterable<Object>`类型，但是你希望的是`Iterable<int>`,此时需要使用`.cast()`来做类型转换

```dart
// bad
    var objs = [1, '2', 3, '4'];
    var ints = objs.where((e) => e is int).cast<int>();
```

上面的方式代码冗长并且创建了两个额外的包装函数以及包含冗余的运行时类型检查，幸好，Dart提供`whereType()`方法来解决这类问题。

```dart
// good
    var objs = [1, '2', 3, '4'];
    var ints = objs.whereType<int>();
```

> 使用`whereType()`很简洁，生成期望的类型序列，并且没有不必要的包装。



<a name="3-7"></a>
#### [DON'T]当相邻的操作支持类型转换时，不要使用cast()。

通常，当您处理一个`iterable`或者`stream`时会对它执行几个转换。最后，您希望生成具有特定类型参数的对象。与其添加对cast()的调用，不如看看现有的转换函数是否支持更改类型。

```dart
// good
    var stuff = <dynamic>[1,2];
    var ints = List<int>.from(stuff)
```

```
// bad
    var stuff = <dynamic>[1,2];
    var ints = stuff.toList().cast<int>();
```

在使用`map()`时，请给它指定一个显式的类型参数，以便它生成所需类型的迭代器。类型推断通常根据传递给map()的函数为您选择正确的类型，但有时仍需需要显式申明。

```dart
// good
    var stuff = <dynamic>[1,2];
    var re = stuff.map<double>((n) => 1 / n);
```

```
// bad
    var stuff = <dynamic>[1,2];
    var re = stuff.map((n) => 1 / n).cast<double>();
```


<a name="3-8"></a>
#### [AVOID] 避免使用cast()

以下对前面规则的更宽松的概括。有时,可能序列操作链中没有可以用来修复对象类型的操作。即使这样，在可能的情况下，也要避免使用cast()“更改”集合的类型。

可以选择以下任何一种方式代替:

- _声明期望的类型_ 在集合创建时就指定期望的类型
- _在获取元素时转换类型_ 如果你在遍历元素，可以在迭代过程中在使用`as`转换类型
- _使用`List.from()`做转换_ 如果最终需要访问集合中的大部分元素，并且不需要原始集合的备份，那么使用List.from()转换它。

`cast()`方法的作用是:返回一个惰性集合，该集合在每次操作时检查元素类型。因此如果你只在几个元素上执行操作，那么懒惰集合是有好处的。但在许多情况下，延迟验证和包装产生的开销超过好处。

> 一个_声明集合期望类型_的例子


```dart
// good
    List<int> singletonList(int value) {
        var list = <int>[];
        list.add(value);
        return list;
    }
```

```
//bad
    List<int> singletonList(int value) {
        var list = [];
        list.add(value);
        return list.cast<int>();
    }
```

> _在获取元素时转换类型_的例子


```dart
// good
    void printEvens(List<Object> objects) {
        for (var n in objects) {
            if((n as int).isEven) print(n);
        }
    }
```

```
// bad
    void printEvens(List<Object> objects) {
        for (var n in objects.cast<int>()) {
            if (n.isEven) print(n);
        }
    }
```

> _使用`List.from()`做转换_的例子


```dart
// good
    int median(List<Object> objects) {
        var ints = List<int>.from(objects);
        ints.sort();
        return ints[ints.length ~/ 2];
    }
```

```
// bad
    int median(List<Object> objects) {
        var ints = objects.cast<int>();
        inst.sort(); 
        return ints[ints.length ~/ 2];
    }
```

当然，这些替代方法并不总是有效，有时`cast()`反而是正确的答案。但是需要注意这个方法有点冒险以及可能无法达到预期——它可能很慢，并且在运行时可能失败。

**[⬆ back to top](#top)**

### 函数

**在Dart中函数也是对象(Object),以下是一些有关函数的最佳实践**


<a name="4-1"></a>
#### [DO]使用函数声明为函数赋名

现代语言都会提到嵌套函数和闭包的重要性。在一个函数内部定义另一个函数是很常见的。在很多情况下，这个函数被立即用作返回值，因此不需要名称。函数表达式很适合在在这种情况下使用。

但是，如果确实需要给它(内部函数)一个名称，请使用函数声明语句而不是将lambda函数绑定到某个变量

```dart
// good
    void main() {
        localFunction() {
            // ...
        }
    }
```

```
// bad
    void main() {
        var localFunction = () {
            ...
        };
    }
```


<a name="4-2"></a>
#### [DON'T] 在`tear-off`函数符合条件的情况下，不要创建`lambda`函数

记住，函数也是对象，因此函数可以直接引用对象上的函数来使用。

在dart中如果您引用某个对象上的方法，但是省略了圆括号，Dart会赋予一个`“tear-off”`—一个与原方法参数相同的闭包(closure)，并在调用它时调用原函数。

因此如果有一个函数调用的方法具有与传递给它的参数相同的参数，则无需手动将调用封装在lambda中。

```dart
// good
    names.forEach(print);
```

```
// bad
    names.forEach((name) {
        print(name);
    })
```

**[⬆ back to top](#top)**

### 参数


<a name="5-1"></a>
#### [DO]使用=符号为命名参数设置默认值

> 因为历史遗留原因，Dart允许`:`和`=`为命名参数设置默认值，为了和可选位置参数保持一致，请使用`=`


```dart
// good
    void insert(Object item, {int at = 0}) { ... }
```

```
// bad 
    void insert(Object item, {int at: 0}) { ... }
```


<a name="5-2"></a>
#### [DON'T] 不要将默认值显式设置为`null`

> 如果你创建了一个可选参数但是没有给予默认值，那么该参数的值会被隐式的设置为`null`，因此你无需显示设置为`null`


```dart
// good
    void error([String message]) {
        stderr.write(message ?? '\n');
    }
```

```
// bad
    void error([String messgae = null]) {
        stderr.write(messgae ?? '\n');
    }
```

**[⬆ back to top](#top)**

### 变量

> 下面是一些在Dart中如何使用变量的最佳实践

<a name="6-1"></a>
#### [DON'T] 不要将初始值设置为null

在Dart中未显示初始化的变量都会被自动初始化为`null`，所以添加`= null`是多余的。

```dart
// good
    int _nextId;

    class LazyId {
        int _id;

        int get id {
            if (_nextId == null) _nextId = 0;
            if (_id == null) _id = _nextId++;

            return _id;
        }
    }
```

```
// bad
    int _nextId = null;

    class LazyId {
        int _id = null;

        int get id {
            if (_nextId == null) _nextId = 0;
            if (_id == null) _id = _nextId++;

            return _id;
        }
    }
```


<a name="6-2"></a>
#### [AVOID] 避免存储你可以计算的变量

> 当设计一个类时,你可以会在一个统一的地方比如构造函数中计算所有的变量，并存储计算的值。


```dart
// bad
    class Circle {
        num radius;
        num area;
        num circumference;

        Circle(num radius)
            : radius = radius,
                area = pi * radius * radius,
                circumference = pi * 2.0 * radius;
    }
```

上面的代码有两个错误的地方：首先这很消耗内存.严格意义上`area`和`circumference`是缓存类型的值，它们可以从已有的其他变量`radius`计算得来。

另一个更严重的问题是，没有考虑缓存失效的问题，你如何知道缓存的值已经失效了，并且需要重新计算。当`radius`改变时，我们应当重新计算`area`和`circumference`，如果没有，那么缓存的值就是错误的。

因此，为了保证准确性我们可能会像下面这样做：

```dart
// bad
    class Circle {
        num _radius;
        num get radius => _radius;
        set radius(num value) {
            _radius = value;
            _recalculate();
        }

        num _area;
        num get area => _area;

        num _circumference;
        num get circumference => _circumference;

        Circle(this._radius) {
            _recalculate();
        }

        void _recalculate() {
            _area = pi * _radius * _radius;
            _circumference = pi * 2.0 * _radius;
        }
    }
```

上面的代码难以编写、维护、测试和阅读。取而代之，在dart中你的实现方式应该如下：

```dart
// good
    class Circle {
        num radius;

        Circle(this.radius);

        num get area => pi * radius * radius;
        num get circumference => pi * 2.0 * radius;
    }
```

现在代码量更少，内存占用也更少，并且不易出错。类成员中只存储了`圆`所必要的数据`radius`。

注意，在某些情况下，你可能需要<br />存储一些运算量较大的值，但你应当只在知道这里存在性能问题才这么做，请谨慎的使用缓存并写上注释解释这里优化的原因。

**[⬆ back to top](#top)**

### 类的成员

> 在Dart中，对象成员包括函数(方法)和数据(实例变量)类型的成员。以下是有关类成员的最佳实践



<a name="7-1"></a>
#### [DON'T] 不要在不必要的时候设置`getter`和`setter`

在Java及C#语言中，成员变量通常是私有的,隐藏在对外提供的`getter`和`setter`函数内(及时内部只是直接访问或者修改该成员变量)。如果您需要在这些成员中做更多的工作，通过将成员变量隐藏在函数中,这样,您可以不需要直接调用原始变量。这是因为，在Java中调用getter方法与访问成员变量是不同的，在c#中访问属性与访问原始成员变量在二进制层面不是兼容的的。

Dart并没有这个限制。成员变量和`getter` /`setter`是相同的。您可以在类中公开成员变量，然后将其封装在getter和setter中，而不需要接触任何使用该字段的代码。

```dart
// good
    class Box {
        var contents;
    }
```

```
// bad
    class Box {
        var _contents;
        get contents => _contents;
        set contents(value) {
            _contents = value;
        }
    }
```


<a name="7-2"></a>
#### [PREFER] 只读属性使用`final`声明

> 如果你有一个字段，在类外部能够访问，但是不希望被修改，一个简单的解决办法是将他标记为`final`


```dart
// good
    class Box {
        final contents = [];
    }
```

```
// bad
    class Box {
        var _contents;
        get contents => _contents;
    }
```

当然，如果你需要在类的构造器外部去设置变量的值，你可能需要使用`private field,public getter`模式(上面bad的例子)但请在需要的情况下才这么做。


<a name="7-3"></a>
#### [CONSIDER] 对于一个简单属性的获取使用`=>`

除了为函数表达式使用`=>`之外，Dart还允许您使用它定义成员变量。这种方式非常适合一些只计算并返回值的成员变量。

```dart
// good
    double get area => (right - left) * (bottom - top);

    bool isReady(num time) => minTime == null || minTime <= time;

    String capitalize(String name) =>
        '${name[0].toUpperCase()}${name.substring(1)}';
```

不要滥用`=>`，这样最终形成的代码很难阅读。如果您的`=>`声明语句超过数行或者包含深度嵌套的表达式(级联和条件运算符是常见的例子)，为了方便阅读，请转而使用以代码块实现的正常写法。

```dart
// good
    Treasure openChest(Chest chest, Point where) {
        if (_opened.containsKey(chest)) return null;

        var treasure = Treasure(where);
        treasure.addAll(chest.contents);
        _opened[chest] = treasure;
        return treasure;
    }
```

```
// bad
    Treasure openChest(Chest chest, Point where) =>
    _opened.containsKey(chest) ? null : _opened[chest] = Treasure(where)
      ..addAll(chest.contents);
```

你也可以在不需要返回值的情况下在函数上(字段及函数)使用`=>`。当setter函数比较简单，并且类中存在使用=>的相应getter时，这是惯用的写法。

```dart
// good
    num get x => center.x;
    set x(num value) => center = Point(value, center.y);
```


<a name="7-4"></a>
#### [DON'T] 不要在不必要的时候使用`this`

JavaScript需要显式地使用`this`来访问当前执行该函数的对象的成员。但是像c++、Java和c#这样并没有这种限制。

只有2中情况下你只需要使用`this`,一个是局部变量与你想访问的成员变量重名:

```dart
// good
    class Box {
        var value;

        void clear() {
            update(null);
        }

        void update(value) {
            this.value = value;
        }
    }
```

```
// bad
    class Box {
        var value;

        void clear() {
            this.update(null);
        }

        void update(value) {
            this.value = value;
        }
    }
```

另一种情况是使用`this`指向命名构造函数

```dart
//bad
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // This won't parse or compile!
  // ShadeOfGray.alsoBlack() : black();
}
```

```dart
//good
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // But now it will!
  ShadeOfGray.alsoBlack() : this.black();
}
```

注意构造初始化时参数赋值是不需要`this`的

```dart
// good
    class Box extends BaseBox {
        var value;

        Box(value)
            : value = value,
                super(value);
    }
```

这看起来很令人惊讶，但这样的写法是可以正常工作的(达到预期)。幸运的是，因为dart存在初始化形式(`initialzing_formals`)的写法，所以这样的对吗相对较少、


<a name="7-5"></a>
#### [DO] 尽量在初始值声明时赋值初始值

如果成员变量不依赖构造函数参数，那么应该在声明时初始化。这会让代码更加简洁，并且避免了在多构造函数情况下忘记初始化它、

```dart
// good
    class Folder {
        final String name;
        final List<Document> contents = [];

        Folder(this.name);
        Folder.temp() : name = 'temporary';
    }
```

```
// bad
    class Folder {
        final String name;
        final List<Document> contents;

        Folder(this.name) : contents = [];
        Folder.temp() : name = 'temporary'; // Oops! Forgot contents.
    }
```

> 当然如果成员变量依赖构造函数参数，或者由不同的构造函数以不同的方式初始化，那不适用于此准则。


**[⬆ back to top](#top)**

<a name="8-1"></a>
## 构造函数

**下面的最佳实践适用于声明类的构造函数。**


<a name="8-2"></a>
#### [DO] 尽量使用构造函数形式初始化字段

许多字段直接从构造函数参数初始化，比如:

```
// bad
    class Point {
        num x, y;
        Point(num x, num y) {
            this.x = x;
            this.y = y;
        }
    }
```

上面为了定义一个字段，我们使用了4次`x`变量，以下是一个更好的方案:

```dart
// good
    class Point {
        num x, y;
        Point(this.x, this.y);
    }
```

这种在构造函数参数中的`this.`语法称为“初始化形式”(`initializing formal`)。值得注意的是，使用它意味着参数在初始化列表中不可见，因此这种写法并不是每次适用。但在当适用的时候，你就应该这么做。


<a name="8-3"></a>
##### [DON'T] 不要为`initializing formals`参数声明类型

如果构造函数参数使用了`this`来初始化成员变量，参数的类型会自动匹配相应成员变量的类型

```dart
// good
    class Point {
        int x, y;
        Point(this.x, this.y);
    }
```

```
// bad
    class Point {
        int x, y;
        Point(int this.x, int this.y);
    }
```


<a name="8-4"></a>
#### [DO] 构造函数body为空时使用`;`而不是`{}`

> 在Dart中，如果构造函数体为空请用冒号结尾


```dart
// good
    class Point {
        int x, y;
        Point(this.x, this.y);
    }
```

```
// bad
    class Point {
        int x, y;
        Point(this.x, this.y) {}
    }
```


<a name="8-5"></a>
#### [DON'T] 不要使用`new`关键字

Dart 2将`new`关键变成可选的。即使是在Dart 1中，它的含义也从未明确过，因为工厂构造函数意味着调用`new`实际上可能依然不会返回一个新的对象。

为了减少迁移的痛苦，目前仍然允许使用`new`关键字，但是请考虑将其弃用并从代码中删除。

```dart
// good
    Widget build(BuildContext context) {
        return Row(
            children: [
            RaisedButton(
                child: Text('Increment'),
            ),
            Text('Click!'),
            ],
        );
    }
```

```
// bad
    Widget build(BuildContext context) {
        return new Row(
            children: [
            new RaisedButton(
                child: new Text('Increment'),
            ),
            new Text('Click!'),
            ],
        );
    }
```


<a name="8-6"></a>
#### [DON'T] 不要重复声明`const`

> 在状态为`const`的上下文环境中，`const`是隐式的可以省略,无需也不应该重复声明。这些上下文环境包括:


- `const`的集合
- `const`的构造函数
- 元数据注解
- `const`变量的初始化
- `switch`表达式--`case`和`:`之间的区域

(此列表中不包括`Default values`，因为Dart的未来版本可能支持非const的`default values`。)

```dart
// good
    const primaryColors = [
        Color("red", [255, 0, 0]),
        Color("green", [0, 255, 0]),
        Color("blue", [0, 0, 255]),
    ];
```

```
// bad
    const primaryColors = const [
        const Color("red", const [255, 0, 0]),
        const Color("green", const [0, 255, 0]),
        const Color("blue", const [0, 0, 255]),
    ];
```

**[⬆ back to top](#top)**

## 错误处理

> 下面是dart中一些捕获和处理异常的最佳实践

<a name="9-1"></a>
#### [AVOID] 避免没有使用`on`语句捕获错误

在没有`on`语句的情况下，catch语句中将捕获`try代码块`中抛出的所有异常.[Pokemon异常处理](https://blog.codinghorror.com/new-programming-jargon/)很可能不是你想要的。你的代码是否正确地处理了StackOverflowError或OutOfMemoryError?如果你在try块中的代码中将错误的参数传递给某个方法，您是希望调试器将错误指向您错误的代码，还是抛出一个有意义的ArgumentError异常?在捕获抛出的`AssertionError`之后，您希望代码中的`assert()`语句自动失效吗?

答案可能是“否”，在这种情况下，您应该过滤捕获的类型。在大多数情况下，您应该有一个on子句，它将您限制到您所知道的和正在正确处理的运行时故障的类型。

在极少数情况下，您可能希望捕获运行时的任何错误。这通常是在框架或底层代码中，这些代码试图将任意应用程序代码与问题隔离开来。即使是这种情况，捕获`Exception`通常也比捕获所有类型异常要好。`Exception`是所有运行时错误的基类，并帮助检测出哪些引起程序错误的错误代码。


<a name="9-2"></a>
#### [DON'T] 在没有`on`语句处理异常时，不要直接丢弃错误

如果您真的觉得需要捕获从代码块中抛出的所有异常，那么就对捕获的异常进行处理。记录它，显示给用户或重新抛出它，但不要直接默默地丢弃错误。

<a name="9-3"></a>
#### [DO] 只在程序错误(`programmatice rrors`)的情况下抛出实现`Error`类的异常

Error类是程序错误的基类。当抛出该类型的对象或它的一个子类(如ArgumentError)时，意味着您的代码中存在bug。当您的API想要向调用者报告它被错误地使用时，抛出一个错误是一个清晰的信号。

相反，如果异常是某种运行时异常，不存在代码的调用错误时抛出此类错误存在误导。此时，应该抛出一个核心异常类(core Exception)或其他类型。


<a name="9-4"></a>
#### [DON'T] 不要对`Error`异常做处理，let's crash

这是从[上面`](#9-3)得出的经验。由于`Error`代表了代码中的错误，因此它应该展开整个调用栈，停止程序，并打印调用栈记录，以便找到并修复错误。

捕获这些类型的错误会破坏程序并掩盖错误。与其添加错误处理代码来在事件发生后处理此异常，不如找到错误的地方并修复最初导致抛出该异常的代码。

<a name="9-5"></a>
#### [DO] 使用`rethrow`关键词重新抛出无法处理的异常

当捕捉到的异常无法处理并且想要重新抛出异常时，请使用`rethrow`关键词而不是`throw`，因为`rethrow`会提供完整的异常调用栈。而`throw`只提供了最后`throw`位置的调用栈。

```dart
// good
    try {
        somethingRisky();
    } catch (e) {
        if (!canHandle(e)) rethrow;
        handle(e);
    }
```

```
// bad
    try {
        somethingRisky();
    } catch (e) {
        if (!canHandle(e)) throw e;
        handle(e);
    }
```

**[⬆ back to top](#top)**

#### 异步

> Dart支持一些异步编程的语言特性。以下dart中有关最佳异步编码的最佳实践。



<a name="10-1"></a>
#### [PREFER] 使用`async/await`优于`Future`

异步代码是出了名的难以阅读和调试，即使在使用像`futures`这样的抽象概念时也是如此。`async/ wait`语法提高了可读性，并且让您在异步代码中正常使用Dart的控制流结构。

```dart
// good
    Future<int> countActivePlayers(String teamName) async {
        try {
            var team = await downloadTeam(teamName);
            if (team == null) return 0;

            var players = await team.roster;
            return players.where((player) => player.isActive).length;
        } catch (e) {
            log.error(e);
            return 0;
        }
    }
```

```
// bad
    Future<int> countActivePlayers(String teamName) {
        return downloadTeam(teamName).then((team) {
            if (team == null) return Future.value(0);

            return team.roster.then((players) {
            return players.where((player) => player.isActive).length;
            });
        }).catchError((e) {
            log.error(e);
            return 0;
        });
    }
```


<a name="10-2"></a>
#### [DON'T] 不要在`async`没有任何作用时使用它

在任何执行与异步相关的操作的函数上使用`async`容易养成习惯。但在某些情况下是没有必要的。如果在不更改函数行为的情况下可以省略异步，那么就省略。

```dart
// good
    Future afterTwoThings(Future first, Future second) {
         return Future.wait([first, second]);
    }
```

```
// bad
    Future afterTwoThings(Future first, Future second) async {
        return Future.wait([first, second]);
    }
```

> `async`在以下几种情况下是很有用的：


- 使用了`await` （这是必要条件）
- 你需要异步地返回一个错误，`async`加`throw`要比`return Future.error(...)`简洁
- 你需要返回一个值，并且将它隐式的包装为`Future`,`async`要比`Future.value(...)`简洁

```dart
// good
    Future usesAwait(Future later) async {
        print(await later);
    }

    Future asyncError() async {
        throw 'Error!';
    }

    Future asyncValue() async => 'value';
```


<a name="10-3"></a>
#### [CONSIDER] 使用高阶函数处理`stream`

> 与对`Iterable`的处理建议一样，`stream`也提供很多同样的高阶函数，并且可以准确处理传输失败，关闭等事件。



<a name="10-4"></a>
#### [AVOID] 避免直接使用`Completer`类

一些刚接触异步编程的人可能想要创建`Future`对象,当Future构造函数不能满足他们的需求时，他们最终使用`Cpmpleter`来完成任务。

```dart
// bad
    Future<bool> fileContainsBear(String path) {
        var completer = Completer<bool>();

        File(path).readAsString().then((contents) {
            completer.complete(contents.contains('bear'));
        });

        return completer.future;
    }
```

Completer适用于两种底层代码:新的异步原语和不使用`Future`的异步代码。大多数其他情况下应该使用async/ wait或`Future.then()`，因为它们更清晰，且更容易处理错误。

```dart
// good
    Future<bool> fileContainsBear(String path) {
        return File(path).readAsString().then((contents) {
            return contents.contains('bear');
        });
    }
```

```dart
// good
    Future<bool> fileContainsBear(String path) async {
        var contents = await File(path).readAsString();
        return contents.contains('bear');
    }
```


<a name="10-5"></a>
#### [DO] 当参数声明类型为`FutureOr<T>`时候，参数可能为`Object`的情况下请用`Future<T>`做类型判断

`FutureOr<T>`的实际类型可能是`Future<T>`或者是纯粹的`T`。当`FutureOr<T>`参数是某个特定类型，比如`Future<int>`或者`int`类型，此时使用 `is int`或者`is FUture<int>`都没关系，因为这两种类型不存在相交关系。

但是如果这个`<T>`参数类型的实际类型是`Object`，此时因为`Future<Object>` 自身实现了`Object`接口，这两种类型存在相交关系。此时判断 `Future is Object`或者`Future is T`时(`T`是可以用`Object`实例化的类型参数) 会返回true,因此在这种情况下，应该提前显式地判断`Future`类型。


```dart
// good
    Future<T> logValue<T>(FutureOr<T> value) async {
        if (value is Future<T>) {
            var result = await value;
            print(result);
            return result;
        } else {
            print(value);
            return value as T;
        }
    }
```

```kotlin
// bad
    Future<T> logValue<T>(FutureOr<T> value) async {
        if (value is T) {
            print(value);
            return value;
        } else {
            var result = await value;
            print(result);
            return result;
        }
    }
```

在这个`bad`的示例中，如果传递一个Future<Object>，它会错误地将其视为一个`bare(没有包装的)`的异步的值。
