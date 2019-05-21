
## Effective Dart: Style Guide
高质量的代码的前提是有良好的代码风格,保持一致的命名规则、代码顺序、以及代码格式化规则可以让我们的代码看起来是同一人写出来的。在整个dart语言的生态中保持一致的代码风格,有利于我们互相学习以及贡献代码。

1. **标识符命名**  
    - [DO] [类型命名使用大写驼峰风格](#1-1)  
    - [DO] [库个源文件命名使用小写加下划线风格](#1-2)  
    - [DO] [import前缀命名使用小写加下划线风格](#1-3)  
    - [DO] [其他命名使用小写驼峰风格](#1-4)  
    - [PREFER] [常量命名使用小写驼峰风格](#1-5)  
    - [DO]  [缩略词大写](#1-6)  
    - [DON'T] [不要使用前缀字符](#1-7)  
2. **代码顺序** 
    - [DO] [将`dart:xx`导入代码放在其他导入之前](#2-1)
    - [DO] [将`package:xx`导入代码放在相对文件导入之前](#2-2)  
    - [PREFER] [将自己的包的引入代码放在其他三方包引入之后](#2-3)  
    - [DO] [导出代码应该放在所有导入代码之后](#2-4) 
    - [DO] [同一类导入代码顺序根据文件或者包名的首字母排序](#2-5) 
3. **格式化**      
    - [DO] [使用`dartfmt`工具格式化你的代码](#3-1)  
    - [CONSIDER] [修改你的代码使之变得对格式化工具友好](#3-2)  
    - [AVOID] [单行代码不超过80个字符](#3-3)  
    - [DO] [使用大括号包裹你的控制流结构](#3-4)

### 标识符命名

#### 在dart中有三种命名方式
- 大写驼峰命名 `UpperCamelCase`
- 小写驼峰命名 `lowerCamelCase`
- 小写加下划线命名 `lower_case_width_underscores`

<a name="1-1"></a>
#### [DO] 类型命名使用*大写驼峰命名*
> 类，枚举，typedef和类型参数应该将每个单词首字母大写（包括第一个单词）并且不使用任何分隔符
```dart
    class SliderMenu { ... }
    
    class HttpRequest { ... }
    
    typedef Predicate<T> = bool Function(T value);
```
> 当类被用来作为元数据注解时也要使用*大写驼峰命名*
```dart
    class Foo {
        const Foo([arg]);
    }

    @Foo(anArg)
    class A { ... }
    
    @Foo()
    class B { ... }
```
> 当注解类的构造函数不需要参数时，可以使用*小写驼峰命名*的常量代替
```dart
const foo = Foo();

@foo
class C { ... }
```
<a name="1-2"></a>
#### [DO] 对库、包、目录以及源文件使用*下划线命名*
> 某些文件系统的是不区分大小写的，所以很多项目要求将文件名设置为小写。使用下划线作为分割符可以确保即使在未来dart语言支持**symbolic imports**,之前的命名依旧是一个有效的命名。( Using underscores as the separator ensures that the name is still a valid Dart identifier, which may be helpful if the language later supports symbolic imports.)

```dart
    // good
    library peg_parser.source_scanner;

    import 'file_system.dart';
    import 'slider_menu.dart';

    // bad
    library pegparser.SourceScanner;

    import 'file-system.dart';
    import 'SliderMenu.dart';
```
<a name="1-3"></a>
#### [DO] 当import包并取别名时,别名需使用*下划线命名*

```dart
    // good
    import 'dart:math' as math;
    import 'package:ng_component' as ng_component;
    import 'package:js/js.dart' as js;

    //bad
    import 'dart:math' as Math;
    import 'package:ng_component' as ngComponent;
    import 'package:js/js.dart' as JS;
```
<a name="1-4"></a>
#### [DO] 其他情况使用*小写驼峰命名*
> 类成员，顶级(全局)定义，变量，参数，命名参数应该大写除第一个单词外的每个单词，并且不使用分隔符

```dart
    var item;

    HttpRequest httpRequest;

    void align(bool clearItems) {
        //...
    }
```
<a name="1-5"></a>
#### [PREFER] 在代码中定义常量时使用*小写驼峰命名*


```dart
    // good
    const pi = 3.14;
    const defaultTimeout = 4000;
    final urlScheme = RegExp('^([a-z]+):');

    class Dice {
        static final numberGenerator = Random();
    }

    // bad
    const PI = 3.14;
    const DefaultTimeout = 1000;
    final URL_SCHEME = RegExp('^([a-z]+):'); 

    class Dice {
        static final NUMBER_GENERATOR = Random();
    }
```

>Note:我们最初对常量使用Java的SCREAMING_CAPS样式。我们改变的原因如下:
> 
> - SCREAMING_CAPS在很多情况下看起来很糟糕，尤其是对于CSS颜色之类的东西来说。
>
> - 常量经常被更改为final的非const变量，这就需要更改名称。
>
> - 枚举类型上自动定义的值属性为const和小写。


<a name="1-6"></a>
#### [DO] 缩写词的长度应该超过两个字母。
> 大写的首字母缩写可能很难读懂，而多个相邻的首字母缩写可能导致名称模糊不清。例如，给定一个以HTTPSFTP开头的名称，就无法判断它是指HTTPS FTP还是HTTP SFTP。
>
>为了避免这种问题，缩略词应该和普通单词一样首字符大写，而特别的双单词缩写比如IO，DB不改变写法。

```dart
    // good
    HttpConnectionInfo
    uiHandler
    IOStream
    Id
    DB

    // bad
    HTTPConnection
    UiHandler
    IoStream
    ID
    Db
```
<a name="1-7"></a>
#### [DON'T] 不要将类型、作用域等属性含义作为命名前缀字符
>  [匈牙利命名法](https://en.wikipedia.org/wiki/Hungarian_notation)以及其他类似的命名方案出现在BCPL时代，当时编译器在帮助您理解代码方面做得并不多。因为Dart可以告诉您声明的类型、范围、可变性和其他属性，所以没有理由将这些属性编码到标识符名称中。
```dart
    // good
    defaultTimeout
    // bad
    kDefaultTimeout
```
**[⬆ back to top](#top)**
## 代码顺序 💧 
> 为了使dart文件的头部保持整洁，我们对于代码顺序有一些规定，每一个区块应该用空白行分割开
<a name="2-1"></a>
#### [DO] 'dart:' 的引入代码放在其他引入之前

```dart
    import 'dart:async';
    import 'dart:html';

    import 'package:bar/bar.dart';
```
<a name="2-2"></a>
#### [DO] 'package:' 的引入代码放在相对路径引入之前

```dart
    import 'package:bar/bar.dart';

    import '../util.dart';
```
<a name="2-3"></a>
#### [PREFER] 将自己的包的引入代码放在其他三方包引入之后

```dart
    import 'package:bar/bar.dart';

    import 'package:my_package/util.dart';
```
<a name="2-4"></a>
#### [DO] 导出代码应该放在所有导入代码之后
```dart
    // good
    import 'src/error.dart';
    import 'src/foo.dart';

    export 'src/error.dart';

    // bad
    import 'src/error.dart';
    export 'src/error.dart';
    import 'src/foo.dart';
```
<a name="2-5"></a>
#### [DO] 同一类导入代码顺序根据文件或者包名的首字母排序

```dart
    // good
    import 'package:bar/bar.dart';
    import 'package:foo/foo.dart';

    // bad
    import 'package:foo/foo.dart';  
    import 'package:bar/bar.dart';
```
**[⬆ back to top](#top)**
## 代码格式化 🔨 
> 和大多数语言一样，Dart编译器会忽略代码中的空格,然而人类不会。拥有一致的空白行风格可以让人在阅读代码时候像编译器一样高效。

<a name="3-1"></a>
#### [DO] 使用dartfmt工具格式化你的代码
> 格式化代码是一项很无聊的工作，在重构时特别耗费时间。幸运的是现在你不必为此担心，我们提供了一个自动化代码格式化工具叫做[dartfmt](https://github.com/dart-lang/dart_style) 🔗 来替代人工处理。

>下面的一些格式化指导是为了解决一些dartfmt工具不能替代你(手动)解决的问题。
<a name="3-2"></a>
#### [CONSIDER] 修改你的代码使之对格式化处理更友好
>格式化工具会尽力而为的格式化你的代码，但是它无法创造奇迹。如果你的代码有很长的标识符，或者很深层的嵌套，不同类型的运算符号混合等等，格式化后的输出代码依然难以阅读。

>这种情况下，请重构或者简化你的代码。推荐你缩短变量名或者将表达式赋值给一个新的变量。换句话说，尽可能的提高你的代码的可读性。将dartfmt视为你的合作伙伴，合作产出漂亮的代码。
<a name="3-3"></a>
#### [AVOID] 单行代码不要超过80个字符
>可读性研究表明长行文字更难阅读，因为你的视线需要从长行的行首移动到行尾。这也是为什么报纸或者杂志使用多行文字来承载内容。

>如果你发现你的一行代码将要超过80个字符，我们的经验是你的代码可能过于冗长需要压缩一点。比较可能的原因是你使用了超长的类名。这时候你应该问自己类名中的每个单词是否都有特殊的含义或者防止与其他类的意义发生冲突，如果可以考虑省略它。

>请注意dartfmt会为你完成99%的工作，但是最后1%的工作需要你来完成。比如将string字符限制在80个以内这种工作需要你自己来完成。

>我们对URL和文件路径做了处理，当这些出现在注释或者字符串（通常是importh和export）中即使超出限制我们也会保留成一行，这样方便搜索给定路径的源文件。
<a name="3-4"></a>
#### [DO] 使用大括号包住所有控制流结构
> 这样做可以避免悬空else问题

```dart
    // good
    if (isWeekDay) {
        print('I am verhappy');
    } else {
        print('Holy shit');
    }

    // bad 
    if (overflowChars != other.overflowChars)
        return overflowChars < other.overflowChars;
```
>下面是对于控制流代码风格的建议

```dart
    // good
    if (arg == null) return defaultValue;

    // good
    if (overflowChars != other.overflowChars) {
        return overflowChars < other.overflowChars;
    }

    // bad 
    if (overflowChars != other.overflowChars)
        return overflowChars < other.overflowChars;
```
**[⬆ back to top](#top)**
#### [DO] 类型命名使用*大写驼峰命名*
>  类，枚举，typedef和类型参数应该将每个单词的首字母大写（包括第一个单词）并且不使用任何分隔符

```dart
class SliderMenu { ... }

class HttpRequest { ... }

typedef Predicate<T> = bool Function(T value);
```
> 当类被用来作为元数据注解时也使用*大写驼峰命名*
```dart
    class Foo {
        const Foo([arg]);
    }

    @Foo(anArg)
    class A { ... }
    
    @Foo()
    class B { ... }
```

> 当注解类的构造函数不需要参数时，可以使用*小写驼峰命名*的常量代替
```dart
const foo = Foo();

@foo
class C { ... }
```



**[⬆ back to top](#top)**