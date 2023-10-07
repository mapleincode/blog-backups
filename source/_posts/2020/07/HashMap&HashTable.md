---
title: HashMap和HashTable的区别
date: 2020-07-06 16:52:05
tags: [ java ]
---



### HashMap 不是线程安全的

HashMap 是 map 接口的实现类，是将键映射到值的对象，其中键和值都是对象，并且不能包含重复键，但可以包含重复值。HashMap 允许 null  key 和 null value，而 HashTable 不允许。

### HashTable 是线程安全 Collection。

HashMap 是 HashTable 的轻量级实现，他们都完成了Map 接口，主要区别在于 HashMap 允许 null  key 和 null value,由于非线程安全，效率上可能高于 Hashtable。

##### 区别如下：

- HashMap允许将 null 作为一个 entry 的 key 或者 value，而 Hashtable 不允许。
- HashMap 把 Hashtable 的 contains 方法去掉了，改成 containsValue 和 containsKey。因为 contains 方法容易让人引起误解。
- HashTable 继承自 Dictionary 类，而 HashMap 是 Java1.2 引进的 Map interface 的一个实现。
- HashTable 的方法是 Synchronize 的，而 HashMap 不是，在多个线程访问 Hashtable 时，不需要自己为它的方法实现同步，而 HashMap 就必须为之提供外同步。
- Hashtable 和 HashMap 采用的 hash/rehash 算法都大概一样，所以性能不会有很大的差异。



作者：rawer
链接：https://www.jianshu.com/p/5c34133ed372
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 关于 WeakHashMap

WeakHashMap 会检查自己的元素是否被依赖。如果这个元素没有被别的变量所依赖，则 WeakHashMap 会把这个元素从 Map 中剔除。这样这个元素就会被垃圾回收。



而 HashMap 和 HashTable 没有这样的行为。

