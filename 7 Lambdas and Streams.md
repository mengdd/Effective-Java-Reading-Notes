# Chapter 7 Lambda表达式和流
本章内容都是Java 8新增特性的使用相关.

## 第42条 优先使用lambdas而不是匿名类
以前, 只有一个抽象方法的接口(或抽象类)被当做function types使用. 它们的实例是函数对象(function objects), 表示功能或者行为.
从JDK1.1开始, 主要的创建函数对象的行为是匿名类(anonymous class).

匿名类很适合经典的需要函数对象的设计模式, 比如策略模式. 举例 -> `Comparator`提供了抽象策略, 匿名类实现具体策略.

在Java 8中, 认为这种只有一个抽象方法的接口值得被特殊对待, 它们现在被称为函数式接口(functional interfaces), 语言允许你用lambda表达式创建这些接口的实例.

Lambda和匿名类的功能类似, 但是更加简洁.

Lambda表达式可以省略参数和返回值的类型, 这是因为编译器会通过类型推断(type inference)推导出来. (不能推导出来的时候需要指明.)

举例: 之前各个枚举行为不同的例子, 可以用lambda简化, 构造的时候传入lambda, 用`DoubleBinaryOperator`保存.

注意: 由于lambda是没有名字和文档的, 如果一个计算不是自解释的, 或是行数较多(对于lambda来说, 一行最好, 三行最多), 就不要放在lambda中了.

通过enum构造传入的参数是在静态环境的, 所以从enum构造传入的lambda不能访问枚举的成员变量.

仍然有一些事情是匿名类可以做, 但是不能被lambda取代的: 
* lambda只被限制在函数式接口(functional interfaces), 所以抽象类, 多个方法的接口仍然要用匿名类.
* 在lambda中, 不能获取自身的引用, `this`关键字指的是enclosing instance. 匿名类中`this`指这个匿名类对象.

lambda和匿名类都不能可靠地序列化和反序列化, 所以, 你应该少做这件事. 如果真的有需要, 用一个私有静态内部类的实例.

## 第43条 优先使用方法引用而不是lambdas
用lambda取代匿名类的主要优势就是简洁, 其实Java还提供了更简洁的生成函数对象的方法: 方法引用(method references).

代码实例: 统计单词个数的方法, 用Map接口的`merge`方法(Java 8):
```
map.merge(key, 1, (count, incr) -> count + incr);
```
改进:
```
map.merge(key, 1, Integer::sum);
```

为了更加简洁, 可以把lambda中的代码提取到一个新方法中, 然后用这个方法引用取代原来的lambda.
但是偶然的情况下, lambda比方法引用更加简洁. 比如: 方法和lambda在同一个类中.

很多方法引用都是静态的, 但是有四种非静态的: 
* bound and unbound instance method references
* classes和arrays的constructor references, 作为工厂对象.

结论: 当方法引用比lambda更加简洁的时候, 就用方法引用, 否则就用lambda.

## 第44条 优先使用标准的函数式接口
有了lambda之后, 模板方法(Template Method)模式就没有吸引力了, 现代的方法是提供一个接收函数对象的静态工厂或者构造函数来达到相同的效果.
更一般地, 你需要写更多的以函数对象作为参数的构造器和方法. 要谨慎选择正确的函数参数类型.

`java.util.function`包中提供了一系列标准的函数式接口(一共43个).

六个基本的函数式接口:
* `UnaryOperator<T>`: 一个参数, 返回值类型和参数相同.
* `BinaryOperator<T>`: 两个参数, 返回值类型和参数相同.
* `Predicate<T>`: 一个参数, 返回一个boolean.
* `Function<T,R>`: 参数和返回值类型不同.
* `Supplier<T>`: 无参数, 有返回值.
* `Consumer<T>`: 有参数, 无返回值.

每个基本接口都有3种变型, 对应基本类型: int, long和double, 接口名字会加上类型的前缀. (6*3=18个)

`Function`接口还有9种变型, 对应结果的不同基本类型. 
如果源和结果都是基本类型, 加上前缀`SrcToResult`, 比如`LongToIntFunction`. (6种).
如果源是基本类型, 结果是对象引用, 加上前缀`SrcToObj`, 比如`DoubleToObjFunction`. (3种).

有三个基本的函数式接口都有双参数版本, 共有9种变型:
* `BiPredicate<T, U>` (1种).
* `BiFunction<T, U, R>`: 有三种返回不同基本类型的变种. (4种).
* `BiConsumer<T>`: 还有接受一个基本类型和一个对象引用的变种. (4种).

最后还有`BooleanSupplier`是一个返回布尔值的Supplier变型.

大多数的标准函数式接口仅是为了提供基本类型(primitive types)支持而存在. 
不要用基本函数式接口搭配装箱基本类型(basic functional interfaces with boxed primitives)来代替基本类型的函数式接口(primitive functional interfaces).

那什么时候应该写自己的函数式接口呢?
* 没有标准的函数式接口能够满足需求. -> 比如参数个数不同, 或者要抛出一个受检异常.
* 有结构相同的标准函数式接口, 有一些情况也应该写自己的函数式接口. 
比如`Comparator<T>`和`ToIntBiFunction<T,T>`结构相同, 但是仍然用`Comparator`, 好处: 名字; 通用协议; 有用的默认方法.

如果你要自己写一个函数式接口而不是用标准的, 你要考虑它是不是和`Comparator`一样拥有(一个或多个)以下特性:
* 它通用, 会得益于有一个描述性的名字.
* 它与一个很强的协议相关.
* 它会得益于自定义的默认方法.

永远记住用注解`@FunctionalInterface`来标记你的函数式接口.

提供方法重载的时候要注意, 不要给同一个方法提供函数式接口在同一个参数位置的重载(有可能会引起二义性). 比如: `ExecutorService`的`submit`方法.
这需要客户端代码进行强转来指明正确的重载.

## 第45条 Use streams judiciously
## 第46条 Prefer side-effect-free functions in streams
## 第47条 Prefer Collection to Stream as a return type
## 第48条 Use caution when making streams parallel