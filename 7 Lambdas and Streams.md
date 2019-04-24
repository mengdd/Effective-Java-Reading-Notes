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

## 第44条 Favor the use of standard functional interfaces
## 第45条 Use streams judiciously
## 第46条 Prefer side-effect-free functions in streams
## 第47条 Prefer Collection to Stream as a return type
## 第48条 Use caution when making streams parallel