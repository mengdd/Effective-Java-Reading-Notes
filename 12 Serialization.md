# Chapter 12 序列化
## 第74条 谨慎地实现Serializable接口
实现Serializable接口而付出的最大代价是, 一旦一个类被发布, 就大大降低了"改变这个类的实现"的灵活性.

如果你接受了默认的序列化形式, 这个类中私有的和包级私有的实例域将都变成导出的API的一部分, 这不符合"最低限度地访问域"的实践原则, 从而它就失去了作为信息隐藏工具的有效性.

序列化会使类的演受到限制, 这种限制的一个例子与流的唯一标识符有关, 通常它也被称为序列版本UID(serial version UID). 如果没有显式声明, 系统会自动生成.

实现Serializable的第二个代价是, 它增加了出现Bug和安全漏洞的可能性. -> 反序列化是一个隐藏的构造器.

实现Serializable的第三个代价是, 随着类发行新的版本, 相关的测试负担也增加了.

为了继承而设计的类应该尽可能少地去实现Serializable接口, 用户的接口也应该尽可能少地继承Serializable接口.

## 第75条 考虑使用自定义的序列化形式
如果没有先认真考虑默认的序列化形式是否合适, 则不要贸然接受.

如果一个对象的物理表示法等同于它的逻辑内容, 可能就适合于使用默认的序列化形式.

即使你确定了默认的序列化形式是合适的, 通常还必须提供一个`readObject`方法以保证约束关系和安全性.

当一个对象的物理表示法与它的逻辑数据内容有实质性的区别时, 使用默认序列化形式会有以下4个缺点:
* 它使这个类的导出API永远地束缚在该类的内部表示法上.
* 消耗过多的空间.
* 消耗过多的时间.
* 会引起栈溢出.

`transient`修饰符: 从序列化形式中省略掉实例域. 反序列化时这些域将被初始化为默认值.

无论你是否使用默认的序列化形式, 如果在读取整个对象状态的任何其他方法上强制任何同步, 则也必须在对象序列化上强制这种同步.

不论你选择了哪种序列化形式, 都要为自己编写的每个可序列化的类声明一个显式的序列版本UID(serial version UID).

## 第76条 保护性地编写readObject方法
`readObject`方法实际上相当于一个公有的构造器, 如同其他的构造器一样, 它也要求注意同样的所有注意事项. 构造器必须检查其参数的有效性, 并且在必要的时候对参数进行保护性拷贝.

编写更加健壮的`readObject()`方法的指导方针:
* 对于对象引用域必须保持为私有的类, 要保护性地拷贝这些域中的每个对象. 不可变类的可变组件就属于这一类别.
* 对于任何约束条件, 如果检查失败, 则抛出一个`InvalidObjectException`异常. 这些检查动作应该跟在所有的保护性拷贝之后.
* 如果整个对象图在被反序列化之后必须进行验证, 就应该使用`ObjectInputValidation`接口.
* 无论是直接还是间接方式, 都不要调用类中任何可被覆盖的方法.


## 第77条 对于实例控制, 枚举类型优先于readResolve
如果单例模式的类加上了`implements Serializable`, 就多了一种创建实例的途径.

readResolve特性允许你用readObject创建的实例代替另一个实例. 对于一个正在被反序列化的对象, 如果它的类定义了一个readResolve方法, 并且具备正确的声明, 那么在反序列化之后, 新建对象上的readResolve方法就会被调用. 然后该方法返回的对象引用将被返回, 取代新建的对象. 在这个特性的绝大多数用法中, 指向新建对象的引用不需要再被保留, 因此立即成为垃圾回收的对象.

可以利用`readResolve`方法保证单例模式. -> 方法忽略被反序列化的对象, 只返回该类初始化时创建好的那个实例.

如果依赖readResolve进行实例控制, 带有对象引用类型的所有实例域都必须声明为`transient`的.

从历史上来看, readResolve方法被用于所有可序列化的实例受控(instance-controlled)的类. 自从Java1.5以来, 它就不再是在可序列化的类中维持实例控制的最佳方法了.

应该尽可能地使用枚举类型来实施实例控制的约束条件.

## 第78条 考虑用序列化代理代替序列化实例
序列化代理模式(serialization proxy pattern): 
* 为可序列化的类设计一个私有的静态嵌套类(序列化代理), 它应该有一个单独的构造器, 其参数类型就是那个外围类. 
* 在外围类中添加writeReplace方法. -> 产生代理类实例.
* 外围类中添加readObject方法. -> 防止伪造.
* 代理类中提供readResolve方法, 返回一个逻辑上相当的外围类的实例. -> 序列化代理转变回外围类的实例.

序列化代理模式的局限性:
* 不能与可以被客户端扩展的类兼容.
* 不能与对象图中包含循环的某些类兼容.
* 序列化代理模式的功能和安全性有性能开销的代价.

总而言之, 每当你发现自己必须在一个不能被客户端扩展的类上编写readObject或者writeObject方法的时候, 就应该考虑使用序列化代理模式.