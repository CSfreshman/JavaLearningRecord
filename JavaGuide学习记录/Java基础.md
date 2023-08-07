

# Java基础

## 基本概念与常识

### Java语言的特点

- 简单，相比C++容易上手和理解
- 面向对象
- 支持多线程
- 平台无关性

### JVM，JDK，JRE

#### JVM

Java虚拟机，JVM针对不同的操作系统有不同的实现。另外JVM不是只有一种，只要满足JVM规范每个公司或组织都可以开发自己的JVM。目前常见的JVM有HotSpot。

JVM和字节码是Java跨平台的关键

#### JDK

JDK，Java Development Kit，Java开发工具包，包含了JRE和一些开发工具，比如jconsole。javap等

#### JRE

JRE，Java Runtime Environment，Java运行时环境，包括了JVM和Java基础类库。

![JDK 包含 JRE](https://oss.javaguide.cn/github/javaguide/java/basis/jdk-include-jre.png)



### Java是编译性语言还是解释性语言？

首先，Java源程序需要通过编译生成字节码文件，这里可以得到Java是编译性语言。但是生成的字节码文件经过JVM转换成机器码的时候是解释执行的，这里表现了解释性语言的特点。所以Java可以认为是编译性和解释性并存的。



## 基本语法

### 注释

单行，多行，文档

### 标识符和关键字

标识符就是一个名字，比如类名，方法名，变量名等

关键字是被赋予了特殊含义的标识符。

比如访问控制关键字：

- private
- default（默认 不需要写出来）
- protected
- public

类，方法和变量修饰：

- abstract，抽象类、方法
- class。类
- extends，，继承
- final，不可变
- implement，实现接口
- interface，接口
- native，本地的
- new
- static
- transient，序列化时被该关键字修饰的可以不用序列化

### 自增，自减

a++,++a,

加号在前面的，需要先对a进行加1操作，之后使用a

加号在后面的，先使用a，然后对a进行+1操作。

自增操作并不是原子操作，包含多个步骤，先取出a的当前值，然后+1，再赋值给a，在多线程的环境下可能会收到影响。

自减操作相同。

```java
a = 1；
a = a++;
```

这段代码执行之后，a的值还是1，这就要从JVM的角度去考虑。JVM的局部变量表和操作数栈。



### 移位操作符

- `<< `左移
- `>>`有符号右移
- `>>>`无符号右移

float，double不支持移位操作，只有int 和 long可以进行移位。byte shot会先转换成int再进行移位操作。

如果移位的位数大于数值所占有的位数怎么办？

比如int4字节32位，如果移位超过了32，会先对32求余再进行移位。比如移动42位，42%32 =10，也就是移动10位。

long同理，对64求余



### continue，break，return区别

continue跳过本地循环

break跳出循环体

return直接方法

## 基本数据类型

### 有哪些？

- byte 1字节 默认值0
- short 2字节 默认值0
- int 4字节 默认值0
- long 8字节 默认值0
- float 4字节 默认值0f
- double 8字节 默认值0d
- char 2字节，unioncode编码
- boolean 理论上只需要一位就可以标识true or false  默认值false

### 基本类型和包装类的区别？

- 用途：除了一些局部变量或者常量会使用基本数据类型之外，对象属性，方法参数，返回值等都会使用包装类。在集合中，泛型只能是包装类，不能是基本数据类型。

- 存储方式：基本数据类型的局部变量存储在虚拟机栈的局部变量表中，非静态（未被static修饰）成员变量存储在堆中。

  包装类属于对象，几乎所有的对象都存储在堆内存中，包装类也不例外。

- 占用空间：包装类占用空间较大。

- 默认值：包装类没有默认值，没赋值就是null，基本数据类型有默认值

- 比较方式：基本数据类型直接==比较就行，基本数据类型和包装类的比较也可以使用==，包装类之间的比较需要使用equals()进行比较。有一个包装了比较特殊，就是BidDecimal，比较的时候需要使用compareTo方法进行比较。

### 包装类的缓存机制

`Byte`,`Short`,`Integer`,`Long` 这 4 种包装类默认创建了数值 **[-128，127]** 的相应类型的缓存数据，`Character` 创建了数值在 **[0,127]** 范围的缓存数据，`Boolean` 直接返回 `True` or `False`。两种浮点数类型的包装类 `Float`,`Double` 并没有实现缓存机制。

```java
Integer i1 = 40;
Integer i2 = new Integer(40);
System.out.println(i1==i2);
```

注意这里返回false，因为i2是new了一个对象，没有使用缓存



### 自动装箱，自动拆箱原理

装箱调用valueOf方法，拆箱调用了xxxValue方法



### 为什么浮点数运算的时候会出现精度丢失的风险？

因为计算机存储数据的时候是以二进制的形式存储的，存储的时候有一个精度的概念，也就是对于无限循环的小数，计算机无法完整存储，只能保留一部分，这样就会出现精度丢失的问题。

### 如何解决精度丢失的问题

使用BigDeciml存储浮点数，比如对于金钱的存储，都需要使用BigDecimal

对于BigDecimal的比较需要使用compareTo方法，加减乘除也要使用特定的方法。

### 超过long整形的数据应该如何表示？

对于较大的数据，可以使用BigInteger来表示，内部使用`int[]`数组来存储任意大小的整形数据。

## 变量

### 成员变量和局部变量的区别？

- 语法不同

  成员变量可以被访问修饰符以及static修饰，局部变量不行

- 默认值不同

  成员变量有默认值，如果被final修饰需要显示赋值

  局部变量没有默认值

- 生存时间不同

  成员变量是对象的一部分，生命周期与对象相同

  局部变量的声明周期和方法相同

- 存储方式不同

  成员变量如果被static修饰，表示静态变量，属于类，存储在方法区。

  成员变量如果没有被static修饰，表示普通成员变量，属于对象实例，存储在堆内存中

  局部变量存储在虚拟机栈的局部变量表中

  

## 方法

### 静态方法为什么不能调用非静态成员？

因为静态方法是属于类的，在类加载的时候就会分配内存，可以通过类名直接俄访问，而非静态成员数据对象实例，只有在对象实例化之后才存在。也就是说，在非静态成员不存在的时候，静态方法就已经存在了，此时调用不存在的非静态成员属于非法操作。



### 静态方法和实例方法的区别？

- 调用方式：
  静态方法可以使用`类名.方法名`的方法调用，或者使用`对象名.方法名`调用，实例方法只能时候后面的方式。静态方法调用不用创建对象
- 可以访问的资源不同：
  静态方法只能访问静态成员。实例方法不存在限制

### 重写和重载

重载发生在同一个类中，方法名必须相同，参数列表不同（类型，个数，顺序等），返回值和访问修饰符可以不同



重写是子类对父类方法的重新改造，外部样子不变，内部逻辑发生改变。

要求方法名，参数列表必须相同

子类返回类型应该与父类相同或者是其子类型

子类抛出的异常可以是父类异常的子类或者相同

子类的方法访问修饰符不能比父类更加严格。比如父类是public，子类也应该是public，父类是private，子类就不能重写



## 面向对象基础

### 面向对象与面向过程的区别？

- 面向过程把解决问题的过程拆成一个个方法，通过一个个方法的执行解决问题。
- 面向对象会先抽象出对象，然后用对象执行方法的方式解决问题。

面向对象开发的程序更易维护，复用，拓展。面向过程的一般来说性能比面向对象更高一些，因为面向对象需要创建、销毁对象等消耗时间？



### 对象实体和对象引用的区别？

对象实体在堆内存中，对象引用在栈内存中。

一个对象引用可以执行0或者1个对象，

一个对象可以有若干个应用执行它

### 对象相等和引用相等？

对象相等通常指的是内容是否相等。

引用相等通常指的是指向的内存地址是否相等。



### 什么是构造方法？有哪些特点？可以被重写或者重载吗？

构造方法是一种特殊的方法，用于完成对象的初始化工作。

一个类没有声明构造方法也可以执行。因为会生成一个默认的无参构造方法。如果自己声明了构造方法，那么默认的构造方法就不存在。

特点：

- 名字与类名相同
- 生成类的时候自动执行，无需调用

不可以被重写，但是可以重载，所有一个类通常可以有多个构造方法。



### 面向对象的特征

#### 封装



#### 继承



#### 多态



### 抽象类和接口的共同点和区别？

相同点：

- 都不可以被实例化
- 都可以由抽象方法（没有方法体）
- 都可以由默认实现的方法（Java8中使用default关键字可以在接口中定义默认方法）

区别：

- 一个类只能继承一个类，但是可以实现多个接口
- 定义方式不同，一个是abstract class， 一个是interface
- 接口中的成员变量只能是public static final类型的，不能被修改且必须有初始值，但是抽象类的成员变量默认default，可以在子类中重新定义



### 深拷贝、浅拷贝、引用拷贝

浅拷贝：
浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），不过，如果原对象内部的属性是引用类型的话，浅拷贝会直接复制内部对象的引用地址，也就是说拷贝对象和原对象共用同一个内部对象。



深拷贝：
深拷贝会完全复制整个对象，包括这个对象所包含的内部对象。也就是创造一个全新的对象。



实现浅拷贝需要实现Cloneable接口，并重写clone方法。clone方法的实现就是调用Object类的clone方法。



实现深拷贝可以通过序列化与反序列化的方式实现，或者在浅拷贝的实现基础上，对被拷贝对象内部的引用类型的对象也进行拷贝



引用拷贝就是两个不同的引用指向同一个对象。

![浅拷贝、深拷贝、引用拷贝示意图](https://oss.javaguide.cn/github/javaguide/java/basis/shallow&deep-copy.png)



## Object

### 常见方法

```java
/**
 * native 方法，用于返回当前运行时对象的 Class 对象，使用了 final 关键字修饰，故不允许子类重写。
 */
public final native Class<?> getClass()
/**
 * native 方法，用于返回对象的哈希码，主要使用在哈希表中，比如 JDK 中的HashMap。
 */
public native int hashCode()
/**
 * 用于比较 2 个对象的内存地址是否相等，String 类对该方法进行了重写以用于比较字符串的值是否相等。
 */
public boolean equals(Object obj)
/**
 * native 方法，用于创建并返回当前对象的一份拷贝。
 */
protected native Object clone() throws CloneNotSupportedException
/**
 * 返回类的名字实例的哈希码的 16 进制的字符串。建议 Object 所有的子类都重写这个方法。
 */
public String toString()
/**
 * native 方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。
 */
public final native void notify()
/**
 * native 方法，并且不能重写。跟 notify 一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
 */
public final native void notifyAll()
/**
 * native方法，并且不能重写。暂停线程的执行。注意：sleep 方法没有释放锁，而 wait 方法释放了锁 ，timeout 是等待时间。
 */
public final native void wait(long timeout) throws InterruptedException
/**
 * 多了 nanos 参数，这个参数表示额外时间（以纳秒为单位，范围是 0-999999）。 所以超时的时间还需要加上 nanos 纳秒。。
 */
public final void wait(long timeout, int nanos) throws InterruptedException
/**
 * 跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念
 */
public final void wait() throws InterruptedException
/**
 * 实例被垃圾回收器回收的时候触发的操作
 */
protected void finalize() throws Throwable { }

```



### == 和 equals的区别？

==，对于基本数据类型来说，==判断是否相等；对于引用类型来说，==判断的 是对象的内存地址

equals，只能用来判断对象是否相等。如果没有重写该方法，就使用==判断，判断的是地址是否相同，也就是是否为同一个对象。

重写了该方法一般来说都是判断两个对象的内容是否相同。



### hashCode()方法的作用

hashCode方法用来获得对象的哈希码，哈希码的作用是确定对象在哈希表中的位置。

将一个对象添加到哈希表中的时候，先根据对象的哈希码计算对象所处的位置，如果该位置没有元素就认为没有重复元素。有元素了就利用equals进行比较。

所以hashCode和equals方法都可以用来判断对象是否相等。hashCode判断的效率更高，但是存在误判的可能。对于两个相同的对象hashCode一定相等，但是对于不同的对象，hashCode可能不相等也可能相等（hash碰撞）。

在使用hashCode判断相等的时候，如果hashCode不相等就认为不相等；如果相等，还需要经过equals进行判断。



### 为什么重写equals()的时候必须重写hashCode()方法

对于两个相等的对象，其hashCode一定也是相等的。也就是说equals判断相等的对象，hashCode也要相等。

如果只重写equals方法，就可能造成两个equals相等的对象，但是hashCode不相等。

这样可能哈希表的冲突。因为哈希表需要保证对象的唯一性，两个相等的对象，由于hashCode不相等， 就会对应哈希表的不同下标，这样就造成哈希表中存在两个相同的对象。



## String

### String为什么是不可变的？设计成不可变的有什么作用？

`String`的核心数据结构`char[]`数组被final修饰，由于是引用类型，不可以指向其他变量，但是数组内容可以变化。不过String并没有提供修改这个字符数组的方法，所以这个字符数组就可以认为是不可变的。

`String`类被final修饰，不能被继承，这样子类就不会破坏String的不变性

### String、StringBuffer、StringBuilder的区别

- 不可变性

  String不可变，StringBuffer，Stringbuilder可变

- 线程安全性

  String由于是不可变的，可以认为常量，所以是线程安全的。

  StringBuffer是线程安全的

  StringBuilder线程不安全

- 性能

  String的修改会生成一个新的String对象，然后将引用指向新的对象。

  StringBuffer需要保证线程安全，性能略低于StringBuilder。



### 字符串拼接使用”+“还是StringBuilder?

使用+拼接字符串底层还是使用StringBuilder进行拼接。特别注意，如果在循环中使用+进行拼接字符串，会造成创建过多的StringBuilder对象。

### 为什么JDK9之后要将String的底层实现由`char[]`变为`byte[]`

新版本的String支持Latin-1和UTF-16两种编码。如果字符串的中的汉字没有超过Lanti-1的范围，就是用Lantin-1编码方案，这样只需要使用一个byte（一字节）表示，但是char需要使用两字节表示，空间占用减少一半。而且绝大多数的字符串对象只包含Latin-1可表示的字符串。

### String的equals方法和Object的equals方法的区别？

Objects的equals默认使用==进行判断

String的equals进行了重写，比较字符串内容是否相等

### 字符串常量池的作用

字符串常量池是JVM为了提升性能和减少消耗特地为String类开辟的一块区域，主要目的是为了避免字符串的重复创建。

字符串常量池存储的是字符串常量的引用，实际的字符串对象存储在堆内存中。

```java
String s1 ="abc";
String s2 = "abc";
System.out.println(s1 == s2); // true
```

比如上面的代码中，s1和s2的引用都指向了字符串常量池中的字符串常量“abc”的引用。

### String s = new String("abc"); 实际上创建几个对象？

如果字符串常量池中不存在常量“abc”的引用，会先在堆中创建字符串常量“abc”，然后在字符串常量池中保存对应的引用，使用构造方法创建String对象，并将引用赋值给s。

如果常量池中已经由了引用，就只需要new一个String对象即可。

### String#intern方法的作用

`String.intern()` 是一个 native（本地）方法，其作用是将指定的字符串对象的引用保存在字符串常量池中，

- 如果字符串常量池中保存了对应的字符串对象的引用，就直接返回该引用。
- 如果字符串常量池中没有保存了对应的字符串对象的引用，那就在常量池中创建一个指向该字符串对象的引用并返回。

### String类型的常量和变量使用+拼接的时候发生了什么？

对于字符串常量的拼接会进行编译器优化，也就是常量折叠，直接将拼接后的字符串常量的引用放入字符串常量池。

如果是变量之间或者常量与变量之间的拼接，会使用StringBuilder。

对于多个字符串的拼接，尽可能使用StringBuilder的append方法进行拼接，避免生成过多的StringBuilder对象。

如果字符串变量被final修饰，则可以认为是常量，可会进行常量折叠。



## 异常

![Java 异常类层次结构图](https://oss.javaguide.cn/github/javaguide/java/basis/types-of-exceptions-in-java.png)

### Exception和Error的区别？

Exception：程序可以处理的异常，可以通过try-catch进行处理。包括Checked Exception和UnChecked Exception。

Error：程序无法处理的错误，比如VirtureMachineError，OutOfMemoryError等

### Checked Exception 和 Unchecked Exception的区别

**Checked Exception** 即 受检查异常 ，Java 代码在编译过程中，如果受检查异常没有被 `catch`或者`throws` 关键字处理的话，就没办法通过编译。

比如IOException，InterruptedException，FileNotFoundException

**Unchecked Exception** 即 **不受检查异常** ，Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。

这种异常实际开发经常遇到：

- `NullPointerException`(空指针错误)
- `IllegalArgumentException`(参数错误比如方法入参类型错误)
- `NumberFormatException`（字符串转换为数字格式错误，`IllegalArgumentException`的子类）
- `ArrayIndexOutOfBoundsException`（数组越界错误）
- `ClassCastException`（类型转换错误）
- `ArithmeticException`（算术错误）

### Throwable类常用方法有哪些？

- `String getMessage()`: 返回异常发生时的简要描述
- `String toString()`: 返回异常发生时的详细信息
- `void printStackTrace()`: 在控制台上打印 `Throwable` 对象封装的异常信息

### try-catch-finally如何使用？finally的代码一定会执行吗？

`try`块：用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。

`catch`块：用于处理 try 捕获到的异常。

`finally` 块：无论是否捕获或处理异常，`finally` 块里的语句都会被执行。当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行。 finally有return语句的话，会执行finally的return然后结束方法。



### 如何使用try-with-resources代替try-catch-fianlly？

对于实现了`java.lang.AutoCloseable`或者 `java.io.Closeable` 的对象可以使用try-with-resource

这样声明的资源，不用在finally块中手动关闭，这些资源会在任何catch和finally块执行之前自动关闭。



### 如何对异常进行处理？

try-catch-finally处理或者排除异常

### throw和throws的区别

- throw用于手动抛出异常，抛出的是异常实例，在方法内部使用

- throws用于声明当前方法可能抛出的异常类型

  throws用于方法名上，后面跟着的是异常类型列表，多个异常类型之间使用逗号分隔

  当方法内可能抛出一个或多个异常，同时没有进行处理（try-catch）的时候就需要使用throws声明这些异常（不受检查的异常可以不用写throws，受检查的异常要写）

  如果方法throws了受检查的异常，那么调用该方法的方法就需要对抛出的异常进行处理

### 异常的使用有哪些需要注意的地方？

- 不要把异常定义为静态变量，每次抛出异常的时候都要手动new一个异常对象抛出

  ```java
  if(判断条件){
  	throw new MyException("异常信息");
  }
  ```

- 抛出的异常信息一定要有意义，方便定位问题

- 使用日志打印异常之后就不要再抛出异常了



## 泛型

### 什么是泛型？有什么作用？

泛型在java的集合中使用非常多，Java集合在设计的时候，参数都是用的是泛型。使用泛型可以增加代码的可复用性和类型安全性。比如在new一个集合实例的时候需要指定泛型，也就确定了这个集合可以存储的对象的类型，放入其他类型的对象就会报错。



### 使用方式有哪些？

泛型一般有三种使用方法。泛型类，泛型接口，泛型方法等。

泛型类需要在类定义的时候定义泛型，然后就可以使用这个泛型，作为成员变量的类型。

泛型接口，与泛型类相同

泛型方法，通常是将泛型作为方法的参数使用

### 项目中如何使用泛型？

集合类需要使用泛型。

对于一些通用的工具，使用泛型可以很好的提升程序的可复用性。比如项目中的想用工具，ResponseUtil，就是用了泛型，可以存放不同的返回对象。



## 反射

### 什么是反射？

通过反射可以获取任意一个类的所有属性和方法，无论这些属性和方法是不是被private修饰的。像Spring这些框架的实现就需要通过反射来完成。通过反射可以在程序执行的过程去分析类以及执行类中的方法。



### 有什么优缺点？

反射机制可以让我们的代码更加灵活。

存在的问题就是存在一定的安全问题。反射可以绕过访问修饰符的限制，以及泛型参数的检查（因为泛型参数的检查通常是在编译期间进行的）。

另外反射也存在一定的性能问题。

### 应用场景？项目中如何使用反射？

在写业务代码的时候可能接触不到反射，但是反射对于Spring、MyBatis这些框架的实现却至关重要，这些框架都大量使用到了反射。

这些框架会使用动态代理，而动态代理又依赖于反射机制。



在代理对象中，想要执行被代理对象的方法，就需要使用反射中的`Method#invoke`方法

### 如何获得Class对象

- 知道类名，直接类名.class
- `Class.forName("类的全路径")`
- 实例对象.getClass()获取
- 通过类加载器.loadClass("类的全路径")获取

## 注解

### 自定义注解如何实现的？

通过`@interface`定义注解

参数只能是public或者default修饰符修饰的

成员参数只能是基本数据类型，String，枚举，Class，数组等。

需要加一些元注解，比如

- @Documented – 注解是否将包含在JavaDoc中
- @Retention – 什么时候使用该注解，表示注解的生命周期
  - RetentionPolicy.SOURCE : 仅存在于源代码中
  - RetentionPolicy.CLASS : 默认策略，在class字节码文件中存在
  - RetentionPolicy.CLASS : 默认策略，在class字节码文件中存在
- @Target – 注解用于什么地方，包括下面一些使用范围
  - ElementType.TYPE: 类、接口、注解、enum
  - ElementType.FIELD: 成员变量、对象、属性、枚举的常量
  - ElementType.METHOD: 方法
  - 还有一些其他的，但是不常用



## SPI

### 什么是SPI？

SPI就是`Service Provider Interface`，字面意思就是“服务者提供的接口”，也就是专门提供给服务提供者或者扩展框架功能的开发者去使用的接口。

通过SPI，可以将服务调用者和服务提供者完全解耦。在Java中使用了SPI机制的有Spring框架，数据库驱动加载，日志接口等。



### SPI和API的区别？

SPI和API都是接口，我认为区别在于实现接口的人不同。

对于API，是由提供者定义接口并实现接口，调用者去使用接口即可。

对于SPI，是由调用者定义接口，由提供者去实现接口。比如某个厂商需要某种零件，交付其他工厂制作，它只需要规定还零件的标准既可以了。

![img](https://oss.javaguide.cn/github/javaguide/java/basis/spi/1ebd1df862c34880bc26b9d494535b3dtplv-k3u1fbpfcp-watermark.png)



对于服务提供者，需要在src目录下创建`META-INF/service`文件夹，新建一个以SPI的全类名为文件名的文件。文件中的内容是接口实现类的全类名.

这里`META-INF/service`是一种约定,比如SpringBoot中的约定大于配置.





### SPI的优缺点？

SPI可以提高接口设计的灵活性,但是也存在一些问题

比如存在多个实现类的时候,遍历所有实现效率较低.





## 序列化与反序列化？

当需要将Java对象保存在磁盘中,或者在网络上传输Java对象的时候,就需要对Java对象进行序列化. 从文件或者网络上还原Java对象就是反序列化.

序列化:将对象转换成二进制字节流的过程

反序列化:将序列化过程中生成的二进制字节流转成对象的过程

### 对应了网路模型中的哪一层？

序列化对应OSI七层模型中的表示层,也就是说对应了TCP/IP协议族的应用层.

### 有些字段不想序列化怎么办？

对于不想被序列化的字段,需要使用`transient`关键字修饰，只能修饰变量，不能修饰类和方法，被该关键字修饰的变量在反序列化的时候会置为默认值。

对于static修饰的变量，因为这些静态变量是属于类的，不属于对象，所以不会被序列化。

### 序列化协议

有XML，JSON这些文本形式的序列化协议，可读性好但是性能差。

JDK自带的序列化，需要实现`Serializable`接口，指定serializableId。

但是存在问题，比如不支持跨语言调用，性能差，以及安全问题。反序列化的数据可以被用户控制，这样攻击者就可以通过构造恶意输入攻击程序。





## I/O流



## Stream流



## BigDecimal详解

使用浮点数会存在精度丢失的风险，尤其是与金钱相关的场合。需要使用BigDecimal来进行浮点数运算。



创建BigDecimal的时候使用`BigDecimal(String val)`或者`BigDecimal.valueOf(double val)`进行创建。

加减乘除均使用BigDecimal提供的方法，add，subtract，multiply，divide等。

比较大小使用compareTo方法，也要使用这个方法判断相等。如果使用equals判断，不仅判断数值，还会判断精度，也就是小数点的位数。

使用setScale方法设置保留几位小数以及保留规则。

## Unsafe详解



## Java代理模式

### 静态代理

代理类聚合一个被代理类，使用的时候使用代理类，代理类的代理方法调用被代理方法，并进行增强处理。



### 动态代理

通过Proxy类和InvocationHandler接口实现。



## 各个版本的新特性

### JDK8新特性

#### 函数式接口



#### Lambda表达式



#### Stream流



#### Optional