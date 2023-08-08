# Java集合

## 集合概述

### 有哪些集合？

Java中的集合主要有两个接口，`Collection`和`Map`，

Collection接口下面有List接口，Set接口和Queue接口，具体的实现类有

- ArrayList
- LinkedList
- Vector
- HashSet
- TreeSet
- LinkedHashSet
- PriorityQueue

等

![Java 集合框架概览](https://oss.javaguide.cn/github/javaguide/java/collection/java-collection-hierarchy.png)

### List、Set、Queue、Map的区别？

- List：有序，可重复
- Set：元素不可重复
- Queue：按照特定的顺序确定元素的先后顺序，比如先进先出的队列，先进后出的栈
- Map：键值对，key不可重复，value不做要求



### 这些集合底层使用的数据结构有哪些？

#### List

- ArrayList，Object[]
- Vector,Object[]
- LinkedList,双向链表

#### Set

- HashSet，Hash实现
- LinkedHashSet,LinkedHashMap实现
- TreeSet，有序唯一，红黑树实现

#### Queue

- PriorityQueue，Object[] 数组来实现二叉堆

#### Map

- HashMap，JDK1.8数组+链表+红黑树，1.7数组+链表
- LinkedHashMap，在HashMap的基础上增加了一条双向链表，可以保持键值对的插入顺序
- TreeMap，红黑树

### 如何选择集合？

如果需要根据key得到value就选用Map接口，一般使用HashMap，要求有序就使用TreeMap，线程安全就是用ConcurrentHashMap

只需要保存元素就是用Collection接口，保证唯一性使用Set，其他就可以使用List



###  Comparable 和 Comparator 的区别

Comparable是一个内部比较器，被实现的类可以通过实现该接口来定义对象之间的自然顺序。需要重写compareTo方法进行比较。当使用Collections.sort方法 的时候，前提是集合存储的元素实现了该接口同时重写了compareTo方法。

Comparator是一个外部比较器，允许我们自定义比较排序的逻辑。



### Collection和Collections的区别？

Collection是集合中的一个顶层接口，下面有Set，List，Queue等接口实现

Collections是一个用来操作集合的工具类

## List

### ArrayList和Array数组的区别？

- ArrayList可以动态扩容，Array不可以。
- ArrayList创建的时候可以不用指定大小，Array必须指定大小
- ArrayList可以使用泛型，保证类型安全。Array没有泛型
- ArrayList只能存储对象，Array可以存储任意类型
- ArrayList可以插入、删除、遍历结合。但是Array只能按照下标访问，没有其他的方法。

### ArrayList和Vector的区别？

ArrayList线程不安全，Vector线程安全，但是比较古老，用的比较少



### ArrayList可以存储null吗

可以，但是存储null可能会操作不当造成空指针异常，

### ArrayList插入和删除元素的时间复杂度？

对于插入：

- 头部插入，需要将之后所有的元素一次向后移动一位，时间复杂度是O（n）
- 尾部插入，如果插入元素没有达到容量上限，O（1）即可。如果达到上限，就需要扩容，扩容之后需要将元素复制到新的容器中，复杂度为O（n），然后执行O（1）的插入操作。
- 对于指定位置的插入，需要将该位置之后的元素后移一位，平均需要移动n/2个元素，复杂度为O（n）

对于删除：

- 对于头部，需要将之后的元素向前移动一位，O（n）
- 尾部删除，O（1）
- 指定位置删除，类似于插入的时候，平均移动n/2个元素，复杂度为O（n）



- 

### LinkedList 插入和删除元素的时间复杂度？

对于头部和尾部的插入删除操作，只需要修改头尾指针即可完成，复杂度为O（1）

指定位置插入删除，需要先找到指定的位置，在执行修改指针的操作，平均查找n/2个元素，O(n)

### LinkedList 为什么不能实现 RandomAccess 接口？

`RandomAccess` 是一个标记接口，表示实现该接口的集合可以随机访问。由于LinkedList底层是链表实现，内存地址不连续，无法通过下标随机访问

### ArrayList 与 LinkedList 区别?

二者都是线程不安全的

- 数据接口不同

- 插入和删除的效率不同

- 是否支持随机访问

- 内存空间占用不同

  ArrayList尾部会预留一些空间。LinkedList每一个元素消耗的空间都比ArrayList的大



### 说一说 ArrayList 的扩容机制吧

插入元素前先检查容量是否满足插入一个元素。不满足就扩容。

初始容量为10，1.5倍扩容

## Set

### 无序性和不可重复性的含义是什么

无序性指的是存储的元素并不是按照元素插入的顺序存储的，而是由数据的hash值决定的`hash = (h=key.hashCode()) ^ (h >>> 16)  `

`index = hash & (length - 1)`



不可重复就是保证Set中元素的唯一性，

### 比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同

三种都可以保证数据的唯一性，同时都是线程不安全的

HashSet、LinkedHashSet、TreeSet的主要区别在于底层的数据结构不同，HashSet的底层数据结构是HashMap，LinkedHashSet底层使用LinkedHashSet，保证元素的先进先出。TreeSet底层使用红黑树，可以自定义排序规则。

## Queue



## Map

### HashMap 和 Hashtable 的区别



### HashMap 和 HashSet 区别



###  HashMap 和 TreeMap 区别



###  HashSet 如何检查重复?



### HashMap 的底层实现

#### JDK1.8 之前

#### JDK1.8 之后（包括1.8）



###  HashMap 的长度为什么是 2 的幂次方



### HashMap 多线程操作导致死循环问题



### HashMap 为什么线程不安全？



### HashMap 常见的遍历方式?



### ConcurrentHashMap 和 Hashtable 的区别



### ConcurrentHashMap 线程安全的具体实现方式/底层具体实现

#### JDK1.8 之前



#### JDK1.8 之后（包括1.8）



### JDK 1.7 和 JDK 1.8 的 ConcurrentHashMap 实现有什么不同？





## 集合使用的注意事项

### 集合转Map



### 集合遍历

#### fail-fast和fail-safe



### 集合转数组

使用集合的`toArray(T[] array)`方法，传入的类型完全一直，长度为0的空数组。



### 数组转集合

使用`Arrays.asList()`可以将数组转成集合，但是不能使用`add/remove/clear`等方法。

可以使用下面一些方法将数组转成集合：

- `new ArrayList(Arrays.asList(T[] array))` 在asList方法外面套一层new方法，这样构造的集合就可以修改了
- 使用stream方式，`Arrays.stream(T[] array).collect(Collectors.toList())`



## 源码分析

### ArrayList



### LinkedList



### Hashmap



### ConcurrentHashMap



### CopyOnWriteArrayList