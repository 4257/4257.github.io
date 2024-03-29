# 对象导论
#### 抽象过程
#### 每个对象都有一个接口
#### 每个对象都提供服务
将对象想象为服务提供者，程序本身是为用户提供服务，而解决问题的方式是调用其他对象提供的服务来实现这一目的  
或创建或寻找理想的服务来解决问题的一系列对象  
对象需要高内聚，指的是当前对象应当仅包含一个通用的实现，不去试图做更多的事情
#### 被隐藏的具体实现
提供类的和消费类
让提供者能任意修改被隐藏的部分，消费者不能直接和任意的去修改类的成员
对象的访问边界  
publlic private protected 默认访问权限(包访问权限)
#### 复用具体实现
当实现了一个可复用的对象的时候
最直接的使用方式就是直接使用该类的一个对象
或者将那个类的一个对象置于一个新设计的类中
也称为组合
#### 继承
继承是对代码的复制
一个基类含有其所有子类所共享的特性和行为
当继承一个类型时，也就创建了新的类型，新的类型包括被继承类型的所有成员(包括被隐藏的成员)和方法
子类具有父类的方法 说明所有可以发送给父类的消息同时也可以发送给子类对象
由于通过发送给类的消息的类型可知类的类型？ 意味着子类和父类具有相同的类型
继承来的新类型可以认为与父类具有相同类型
覆盖 拓展接口
##### 是一个与像是一个关系
#### 伴随多态的可互换对象
把一个对象不当作所属的特定类型来对待，而是当作基类的对象来对待，可以编写出不依赖特定类型的代码
多态板伴随继承
后期绑定 java使用一段特殊代码来替代绝对地址调用 代码使用在对象中存储的信息来计算方法体的地址
默认就是动态绑定 C++使用virtual来单独执行需要动态绑定的函数
当子类类型的消息被发送给一个匿名的父类类型时，会基于父类的实际类型产生正确的行为
在OOP编程中，程序直到运行时才能确定代码的地址
#### 单根继承结构
所有的类都继承于单一的基类Object
使得垃圾回收变得简单一些
#### 容器
选择合适的容器
#### 参数化类型
编译器可以自动定制作用于特定类型上的类
#### 对象的创建和生命周期
对于知道类型和大小的类型 将对象置于栈中
对于不知道需要多少对象 生命周期如何 具体类型的 在堆中创建
#### 异常处理
#### 并发编程
共享资源和锁
#### java与internet
# 一切皆对象
#### 用引用操作对象
操纵的标识符是对象的一个引用
#### 特例:基本类型
在栈中创建的类型
基本类型的大小是确定的
基本类型都有可以在堆中创建一个非基本对象 用来表示它的基本类型
#### 永远不要销毁对象
gc会自动销毁new的对象
#### 创建新的数据类型:类
类的成员如果是基本数据类型 则具有默认值
如果成员是对某个对象的引用 那么必须初始化该引用
局部变量没有默认值 需初始化
#### 构建一个java程序
当成员或方法是static关键字时 可以不生成对象而使用类名.成员/方法调用
staic字段对每个类来说只有一份储存空间 非staic字段对每个对象有一个储存空间
staic最重要的用法是不生成对象就能调用
### 第三章 操作符
#### 赋值
主数据类型(基本数据类型)赋值就是值得复制 int A = 4  
引用类型(对象)赋值得时候是操作其引用 obj A = obj B 类似AB指向同一个地址 A和B都会指向最初B指向的对象  
#### 自动递增和递减
递增或者递减符号在前说明 先自增/减
递增或者递减符号在后说明 先使用变量
#### 关系操作符
`==`操作符比较对象实际上的是对象的引用 
可以使用`equels`比较两个对象实际的值

### 第四章 初始化和清除
#### 用构建器自动初始化
类的同名方法 会在new obj()发配空间的时候对对象进行初始化
```java
class obj{
  obj(){//Initialization method}
  obj(arg){//overload method} //函数重载
  void test(){}
  void test(arg){}
}
```
类里面存在一个编译器生成的无参构建器  
如果手动常见了一个有参数的构建器 则编译器不会自动生成构建器 new对象的时候必须使用有参构建器
#### this关键字
this代表本身会是当前对象的引用
#### 成员初始化
基本类型作为类的成员的时候 都会保证获得一个初始值 0 false null 而引用类型则为null  
方法内部的基本类型则需要手动初始化
#### 规定初始化
最直接的是在类内部定义的时候就直接初始化引用类型和基本类型 而C++不能直接初始化 会强制在构造函数初始化
#### 构建器初始化
由于可以在运行期间嗲用方法 从而对决定初始化值 但是不妨碍自动初始化值的进行 他在构建器运行前就会发生 参考成员初始化
#### 初始化顺序
在一个类里面 初始化的顺序是由变量在类内的定义顺序决定的 即使变量定义分布在类的中间或者最后  
都会在任何方法之前先得到初始化 包括构建器调用之前
#### 静态数据的初始化
若数据是静态的（static） 那么同样的事情就会发生 如果它属于一个基本类型（主类型） 而且未对其
初始化 就会自动获得自己的标准基本类型初始值 如果它是指向它是指向一个对象的句柄 那么除非新建一个对象并将句柄同它连接起来 否则就会得到一个空值（NULL）
static初始化只有在必要的时候才会进行 只有在创建或者访问了一个包含static的类的时候
初始化首先是static 接着是非static
### 隐藏实施过程
如何将发生变化的东西与保持不变的东西分隔开  
#### 5.3接口与实现
使用访问控制 防止一些私有的方法被认为是接口的一部分而被调用
### 第六章 类再生
#### 
