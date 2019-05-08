# Effective-Java-Reading-Notes
Effective Java读书笔记.

## Repo说明
* 第二版笔记: branch: 2nd-edition, tag: v2.
* 第三版笔记: branch: 3rd-edition, tag: v3.
* 默认的master分支上是第三版的笔记.
* 点击目录中章节标题可以进入对应章节笔记.

## 目录
### [2 创建和销毁对象](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/2%20Creating%20and%20Destroying%20Objects.md)
* Item 1: 考虑用静态工厂方法代替构造器
* Item 2: 遇到多个构造器参数时要考虑用Builder
* Item 3: 用私有构造器或者枚举类型强化Singleton属性
* Item 4: 通过私有构造器强化不可实例化的能力
* Item 5: 优先使用依赖注入而不是直接绑定资源
* Item 6: 避免创建不必要的对象
* Item 7: 消除过期的对象引用
* Item 8: 避免使用终结方法和清理器
* Item 9: 优先使用`try-with-resources`而不是`try-finally`
### [3 对于所有对象都通用的方法](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/3%20Methods%20Common%20to%20All%20Objects.md)
* Item 10: 覆盖`equals`时请遵守通用约定
* Item 11: 覆盖`equals`时总要覆盖`hashCode`
* Item 12: 始终要覆盖`toString`
* Item 13: 谨慎地覆盖`clone`
* Item 14: 考虑实现`Comparable`接口
### [4 类和接口](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/4%20Classes%20and%20Interfaces.md)
* Item 15: 使类和成员的可访问性最小化
* Item 16: 在公有类中使用访问方法而非公有域
* Item 17: 使可变性最小化
* Item 18: 组合优先于继承
* Item 19: 要么为继承而设计, 并提供文档说明, 要么就禁止继承
* Item 20: 接口优于抽象类
* Item 21: 为了后代设计接口
* Item 22: 接口只用于定义类型
* Item 23: 类层次优于标签类
* Item 24: 优先考虑静态成员类
* Item 25: 限制源文件为单个顶级类
### [5 泛型](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/5%20Generics.md)
* Item 26: 不要使用原生态类型
* Item 27: 消除非受检警告
* Item 28: 列表(lists)优先于数组(arrays)
* Item 29: 优先考虑泛型
* Item 30: 优先考虑泛型方法
* Item 31: 利用有限制通配符来提升API的灵活性
* Item 32: 谨慎地结合泛型和可变参数
* Item 33: 优先考虑类型安全的异构容器
### [6 枚举和注解](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/6%20Enums%20and%20Annotations.md)
* Item 34: 用enum代替int常量
* Item 35: 用实例域代替序数
* Item 36: 用EnumSet代替位域
* Item 37: 用EnumMap代替序数索引
* Item 38: 用接口模拟可扩展的枚举
* Item 39: 注解优先于命名模式
* Item 40: 坚持使用Override注解
* Item 41: 用标记接口定义类型
### [7 Lambda表达式和流](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/7%20Lambdas%20and%20Streams.md)
* Item 42: 优先使用lambdas而不是匿名类
* Item 43: 优先使用方法引用而不是lambdas
* Item 44: 优先使用标准的函数式接口
* Item 45: 谨慎使用streams
* Item 46: 优先使用streams中没有副作用的方法
* Item 47: 优先使用Collection而不是Stream作为返回值
* Item 48: 把流变为并行时要多加小心
### [8 方法](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/8%20Methods.md)
* Item 49: 检查参数的有效性
* Item 50: 必要时进行保护性拷贝
* Item 51: 谨慎设计方法签名
* Item 52: 慎用重载
* Item 53: 慎用可变参数
* Item 54: 返回零长度的数组或集合, 而不是null
* Item 55: 明智地返回optionals
* Item 56: 为所有导出的API元素编写文档注释
### [9 通用程序设计](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/9%20General%20Programming.md)
* Item 57: 将局部变量的作用域最小化
* Item 58: for-each循环优先于传统的`for`循环
* Item 59: 了解和使用类库
* Item 60: 如果需要精确的答案, 请避免使用`float`和`double`
* Item 61: 基本类型优先于装箱基本类型
* Item 62: 如果其他类型更适合, 则尽量避免使用字符串
* Item 63: 当心字符串连接的性能
* Item 64: 通过接口引用对象
* Item 65: 接口优先于反射机制
* Item 66: 谨慎地使用本地方法
* Item 67: 谨慎地进行优化
* Item 68: 遵守普遍接受的命名惯例
### [10 异常](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/10%20Exceptions.md)
* Item 69: 只针对异常的情况才使用异常
* Item 70: 对可恢复的情况使用受检异常, 对编程错误使用运行时异常
* Item 71: 避免不必要地使用受检的异常
* Item 72: 优先使用标准的异常
* Item 73: 抛出与抽象相对应的异常
* Item 74: 每个方法抛出的异常都要有文档
* Item 75: 在细节消息中包含能捕获失败的信息
* Item 76: 努力使失败保持原子性
* Item 77: 不要忽略异常
### [11 并发](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/11%20Concurrency.md)
* Item 78: 同步访问共享的可变数据
* Item 79: 避免过度同步
* Item 80: executor, task和streams优先于线程
* Item 81: 并发工具优先于`wait`和`notify`
* Item 82: 线程安全性的文档化
* Item 83: 慎用延迟初始化
* Item 84: 不要依赖于线程调度器
### [12 序列化](https://github.com/mengdd/Effective-Java-Reading-Notes/blob/master/12%20Serialization.md)
* Item 85: 优先考虑非Java序列化的其他选择
* Item 86: 谨慎地实现`Serializable`接口
* Item 87: 考虑使用自定义的序列化形式
* Item 88: 保护性地编写`readObject`方法
* Item 89: 对于实例控制, 枚举类型优先于`readResolve`
* Item 90: 考虑用序列化代理代替序列化实例

## Resources
* Source code of the book: [jbloch/effective-java-3e-source-code](https://github.com/jbloch/effective-java-3e-source-code)