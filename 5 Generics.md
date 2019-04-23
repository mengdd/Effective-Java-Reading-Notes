# Chapter 5 泛型

## 第23条 不要使用原生态类型
类或接口的声明中如果有类型参数, 就是泛型类或泛型接口, 统称泛型.
比如List<E>接口.

每个泛型都定义一个原生态类型(raw type), 即不带任何实际类型参数的泛型名称. 
例如, 与List<E>相对应的原生态类型是List. 与Java平台没有泛型之前的接口类型List完全一样.

如果使用原生态类型, 就失掉了泛型在安全性和表达性方面的所有优势. 它的存在只是为了兼容泛型出现之前的旧版本的代码.

注意: 使用`List<Object>`仍然是可以的.
区别就是raw type逃避了泛型检查, 而`List<Object>`则明确地告诉编译器, 它能够有任意类型的对象. 

一个`List<String>`可以传给类型为`List`的参数, 但不能传给`List<Object>`.

如果要使用泛型, 但不确定或者不关心实际的类型参数, 可以使用一个问号(无限制的通配符类型)代替. 比如`Set<?>`.

但是使用了这个通配符的缺点就是, 你无法将任何元素(除了null)插入到`Collection<?>`中, 而且根本无法猜测你会得到哪种类型的对象. 
要是无法接受这些限制, 可以使用泛型方法(见30条)或者有限制的通配符类型(见31条).

不要在新代码中使用原生态类型, 有两个小小的例外:
* 在类文字(class literal)中必须使用原生态类型. 比如List.class.
* 使用`instanceOf`的时候: 比如`o instanceOf Set`.


## 第27条 消除非受检警告
用泛型编程时, 有可能会收到很多编译器警告, 要尽可能地消除每一个非受检警告.

有一些根据提示即可消除, 另一些比较难消除.

如果无法消除警告, 但可以证明引起警告的代码是类型安全的, 可以用
`@SuppressWarnings("unchecked")`注解来禁止这条警告. 并加上注释解释为什么是安全的.

如果无法保证安全, 编译时禁止了警告, 运行时还是会抛出`ClassCastException`.

如果明知道安全却不做处理, 没有加Suppress注解, 那么当新出现一条可能有问题的警告时, 新的警告会淹没在所有的错误警告中.

`SuppressWarnings`可以用在任何粒度的级别中. 应该尽量在小范围内使用. 所以实践中常常会额外声明一个局部变量来加上这个注解, 以缩小注解范围.

## 第28条 列表(lists)优先于数组(arrays)
数组与泛型相比, 有两个重要的不同点:
首先:
* 数组是协变的(covariant). -> `Sub[]`是`Super[]`的子类型.
* 泛型是不可变的(invariant). -> `List<Type1>`和`List<Type2>`没有子类型关系.

所以有些类型错误的问题用数组可能要在运行时才能发现, 而用列表在编译时就发现了.

第二大区别:
* 数组是具体化的(reified), 在运行时才知道并检查元素类型约束.
* 泛型是通过擦除(erasure)来实现的. 在编译时强化类型信息, 并在运行时丢弃(或擦除)类型信息. 擦除就是使泛型可以与没有使用泛型的代码随意进行互用.

基于上述这些根本的区别, 因此数组和泛型不能很好地混合使用.

当你得到泛型数组创建错误时, 最好的解决办法通常是优先使用集合类型`List<E>`, 而不是数组类型`E[]`, 这样可能会损失一些简洁性, 但是换回的却是更高的类型安全性和互用性.


## 第29条 优先考虑泛型
举了一个堆栈实现的例子, 开始是用Object类型.

将这个类泛型化:
* 给它的声明加类型参数.
* 用类型参数替换所有的Object类型.
* 解决不能创建泛型数组的问题: 1.创建Object的数组并强转为`E[]`; 2.将声明`E[]`改为`Object[]`, 在pop单个元素的时候强转为E. 因为这两种情况下都可以保证安全性, 所以在最小的范围加上`SuppressWarnings`.

改造后不需要客户端强转.

有一些泛型限制了可允许的类型参数值. 比如`<E extends Delayed>`要求实际的类型参数E必须是`Delayed`的一个子类型. 
此时E被称作有限制的类型参数(bounded type parameter). 

注意: 每个类型都是它自身的子类型.


## 第30条 优先考虑泛型方法
就如类可以从泛型中受益一般, 方法也一样.

静态工具方法尤其适合于泛型化.

声明类型参数的参数列表位于方法修饰符和返回值类型之间.

泛型方法的一个显著特性是, 无需明确指定类型参数的值, 不像调用泛型构造器的时候是必须指定的. 
编译器通过检查方法参数的类型来计算类型参数的值, 这个过程叫做类型推导(type inference).

利用这个特点, 可以利用静态工厂方法来简化泛型构造器的调用.

总而言之, 泛型方法优先于需要客户端来强转参数和返回值的方法.

## 第31条 利用有限制通配符来提升API的灵活性
参数化类型是不可变的(invariant). 对于两个不同的类型Type1和Type2而言, `List<Type1>`和`List<Type2>`没有继承关系.
比如`List<String>`不是`List<Object>`的子类型.

但是有时候可能需要更灵活的应用场景, Java提供了有限制的通配符类型(bounded wildcard type).

举例: 堆栈实现中的两个方法:
```
public void pushAll(Iterable<? extends E> src)

public void popAll(Collection<? super E> dst)
```

为了获得最大限度的灵活性, 要在表示生产者或者消费者的输入参数上使用通配符类型.

助记符:
**PECS**表示`producer-extends, consumer-super`.

注意不要把bounded wildcard types作为返回值.

所有的comparable和comparator都是消费者.

```
// Two possible declarations for the swap method
public static <E> void swap(List<E> list, int i, int j); // unbounded type parameter
public static void swap(List<?> list, int i, int j); // unbounded wildcard

```
哪种更好呢? 对于API来说, 第二种更好 -> 让API更简单, 灵活. 
如果一个参数类型在方法声明中只出现一次, 就用一个wildcard来替代它.
swapHelper -> 把复杂的泛型内化.


## 第32条 谨慎地结合泛型和可变参数
泛型和可变参数都是Java 5的时候添加的, 但是它们却不能很好地一起用.

可变参数的实现实际上是创建了一个数组, 而这个数组实际上又是可见的, 所以当你使用的时候有泛型或参数化类型的可变参数的时候, 会得到令人困惑的编译警告.
这是因为几乎所有的泛型和参数化类型都是non-reifiable的(runtime信息比compile-time少),
所以编译器会在这种声明的时候警告heap pollution: 不能保证类型安全. 

**把一个值保存在泛型的可变参数数列中是不安全的.**

那么为什么声明泛型的数组是非法的, 而这种泛型可变参数声明是合法的呢? 实际上在实践中是有用的, 所以语言设计者保留了它.
Java类库中: `Arrays.asList(T...a)`, `Collections.addAll(Collection<? super T> c, T... elements)`, `EnumSet.of(E first, E... rest)`.
这些类库方法是类型安全的.

在Java 7之前, 对泛型可变参数的警告只能在客户端通过`@SuppressWarnings("unchecked")`来消除, 
Java 7加上了`SafeVarargs`注解, 方法的作者用来承诺安全性.

一个有泛型可变参数的方法, 满足了下面两个条件就是安全的:
* 不存储可变参数数组中的任何东西.
* 不会把这个数组暴露给不受信任的代码.
如果违反了就应该修复, 然后标记`@SafeVarargs`, 这样方法的使用者就不会因为奇怪的编译警告而迷惑了.

还有一种选择是, 用List参数来代替.

## 第33条 优先考虑类型安全的异构容器
泛型最常用于集合, 限制每个容器只能有固定数目的类型参数.
一般来说, 这种情况正是你想要的, 比如一个Set只有一个类型参数, 表示元素类型; Map有两个类型参数, 表示建和值的类型.

但是有时候你会需要更多的灵活性, 有一种方法可以做到这一点: 
将键进行参数化而不是将容器进行参数化. 然后将参数化的键提交给容器, 来插入或获取值. 用泛型系统来确保值的类型与它的键相符.

举例:
```
public class Favorites {
public <T> void putFavorite(Class<T> type, T instance);
public <T> T getFavorite(Class<T> type);
}
```

Favorites实例是类型安全的, 同时也是异构的(heterogeneous): 它的所有键都是不同类型的. 所以Favorites被称作类型安全的异构容器.

Favorites的内部实现用了`HashMap<Class<?>, Object>`, `getFavorite()`方法的实现用了动态转换: `type.cast()`.

为了确保类型约束, 可以在`putFavorite()`方法中加入动态转换, 检验instance是否真的是type所表示的类型的实例. 

java.util.Collections中有一些集合包装类采用了同样的技巧. (checkedSet, checkedList, checkedMap).
