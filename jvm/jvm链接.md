
# 链接

链接一个类，涉及验证准备类或接口，它的超类、超接口、元素类型（如果为一个数组类型）。链接还涉及解析类或接口中的符号引用，虽然并不一定与类或符号链接同时进行。

链接发生：
链接之前，类或接口已经完整载入
初始化之前，类或接口已经完成验证与准备
链接期间检测到的错误将会在程序执行某些需要链接时抛出。
动态计算常量的符号引用直到ldc等引用它的指令被执行，或引用它的启动方法作为静态参数被调用时不会解析。
动态计算调用位置的符号引用直到引用它的启动方法被调用时，不会解析。
jvm实现可以选择懒惰链接策略，也可以选择积极链接策略。
但，只有当使用到被链接到的类或接口时，才会抛出链接错误。

## 验证
验证确保类或接口的二进制表示结构正确。
验证可能导致额外的类与接口被加载，但是不会导致它们被验证或准备。

## 准备
准备涉及为类或接口创建静态字段并且使用默认值初始化这些字段。
此处并不要求执行任何jvm代码。
静态字段的显示初始化作为初始化的部分执行，而不是准备。

准备阶段，jvm施加的载入约束。
载入约束，载入类重写超类、超接口的方法的参数与返回值类型，被载入类或超类的类加载期加载等价。
对于没有被重载的实例方法，选择得到的方法，其形参、返回值类型，必须与超接口声明的方法，使用各自的类加载器得到的加载情况相同。

## 解析

许多jvm指令（anewarray、checkcast、getfield、getstatic、instanceof、invokedynamic、invokeinterface、invokespecial、invokestatic、invokevirtual、ldc、ldc_w、ldc2_w、multianewarray、new、putfield以及oytstatic）依赖运行时常量池中的符号引用。执行这些指令需要解析符号引用。最初，运行时常量池中的所有符号引用都为未解析状态。



对动态计算常量的符号引用，启动方法只会被执行一次。

### 类与接口解析

解析个来源于D的符号引用，指向使用N标记的类或接口C，步骤如下：

1. D的定义类加载器被用于创建用N标记的类或接口。该类或接口为C。

2. 如果C为数组类并且它的元素类型为reference类型，对于该类或接口的符号引用表示的类或接口的解析将会被解析。

3. 最后，访问控制被应用于这个来自D到C的访问。


如果步骤1与步骤2成功，但是3失败，那么C依然有效且可用，只是D被禁止访问C。


### 字段解析

解析一个来源D的对类或接口C中的字段的符号引用，给定该字段的C的符号引用必须首先被解析。所以，任何在解析类或接口引用时被抛出的异常都会作为解析字段解析失败的结果被抛出。如果到C的引用成功被解析，字段解析过程失败产生的异常其本身将会被抛出。

当解析字段引用时，字段解析首先试图在C与其超类中找寻被引用字段：
- 如果C声明了字段引用指定的名称与描述符对应的字段，字段查找成功。被声明的字段为字段查找的结果。
- 否则，字段查找将会被递归应用到指定类或接口C的直接超接口
- 否则，如果C具有超类S，字段查找递归应用到S
- 否则，字段查找失败


接着，字段解析的结果决定于：
- 如果字段查找失败，字段解析抛出NoSuchFieldError。
- 否则，字段查找成功，访问控制将被应用于从D到查找到的字段的访问。
- 如果访问控制失败，字段解析失败
- 否则，应用加载约束



### 方法解析
方法的符号引用不能来自接口
解析对应类于其超类
解析对应接口与指定类

### 接口方法解析

### 方法类型与方法句柄解析

解析对方法类型的符号引用类似于解析对类或接口的符号引用，这些类与接口的名称对应方法描述符中给定的类型。


### 动态计算常量与动态计算调用点解析


## 访问控制

## 方法重写

## 方法选择

在invokevirtual、invokeinterface指令执行期间，方法将会根据位于栈上对象的运行时类型，以及被指令在过去解析的方法所选择。


如果类包含实例方法的重写，使用此方法。
如果类包含超类，在超类中找寻重写的方法。
确定猜的最大确定超接口方法。

