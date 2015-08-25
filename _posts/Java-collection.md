title: Java容器知识整理
date: 2015-06-26 10:05:52
categories: Develop
tags: [Java,interview]
---

###一图胜千言
![image][1]
其中用绿色填充的为常用的类，需重点掌握。

<!-- more --> 
###接口简介
Java容器的最上层都是以接口的形式出现,具体实现由子接口完成。举个栗子，常见的如

     Map<Integer,String> map = new HashMap<Integer, String>();

####Iterator
迭代器,用于遍历容器，JDK源码如下：

    package java.util;
    import java.util.function.Consumer;
    public interface Iterator<E> {
        boolean hasNext();
        E next();
        default void remove() {
            throw new UnsupportedOperationException("remove");
        }
        default void forEachRemaining(Consumer<? super E> action) {
            Objects.requireNonNull(action);
            while (hasNext())
                action.accept(next());
        }
    }

常见用法：
 

    Iterator iter = l.iterator();
      while(iter.hasNext()){
       String str = (String) iter.next();
       System.out.println(str);
      }

  
####Collection
存放独立元素的序列。Collection下又有三个子接口，List,Set,Queue。
#####List
一个有序的Collection（也称序列），元素可以重复。确切的讲，列表通常允许满足 e1.equals(e2) 的元素对 e1 和 e2，并且如果列表本身允许 null 元素的话，通常它们允许多个 null 元素。实现List的有：ArrayList、LinkedList、Vector、Stack等。
#####Set
一个不包括重复元素（包括可变对象）的Collection，是一种无序的集合。Set不包含满 a.equals(b) 的元素对a和b，并且最多有一个null。实现Set的接口有：EnumSet、HashSet、TreeSet等。
#####Queue
一种队列则是双端队列，支持在头、尾两端插入和移除元素，主要包括：ArrayDeque、LinkedBlockingDeque、LinkedList。另一种是阻塞式队列，队列满了以后再插入元素则会抛出异常，主要包括ArrayBlockQueue、PriorityBlockingQueue、LinkedBlockingQueue。

####Map
存放key-value型的元素对。

###常见容器与工具类
####ArrayList
数据结构采用的是线性表，优势是访问和查询十分方便，但添加和删除的时候效率很低。
####LinkedList
数据结构采用的是链表，优势是删除和添加的效率很高，但随机访问元素时效率较ArrayList类低。
####HashSet
数据结构采用的是散列表，主要是设计用来做高性能集运算的，例如对两个集合求交集、并集、差集等。集合中包含一组不重复出现且无特性顺序的元素。其值是不可重复与无序的。
####TreeSet
数据结构使用的是红黑树，性能上低于HashSet，用于排序。
####HashMap
数据结构使用的是散列表，是最常用的是Collection
####TreeMap
与TreeSet同理，用于排序。

####Arrays、Collections
这两者可以理解成工具类，提供一些处理容器类静态方法，比如二分查找，排序等等。
###常见的容器比较

####ArrayList VS Vector
 - ArrayList在内存不够时默认是扩展50% + 1个，Vector是默认扩展1倍。
 - Vector提供indexOf(obj, start)接口，ArrayList没有。
 - Vector属于线程安全级别的，但是大多数情况下不使用Vector，因为线程安全需要更大的系统开销。

没特殊需求，一般用ArrayList

####ArrayList VS LinkedList
 - 因为Array是基于索引(index)的数据结构，它使用索引在数组中搜索和读取数据是很快的。Array获取数据的时间复杂度是O(1),但是要删除数据却是开销很大的，因为这需要重排数组中的所有数据。

 - 相对于ArrayList，LinkedList插入是更快的。因为LinkedList不像ArrayList一样，不需要改变数组的大小，也不需要在数组装满的时候要将所有的数据重新装入一个新的数组，这是ArrayList最坏的一种情况，时间复杂度是O(n)，而LinkedList中插入或删除的时间复杂度仅为O(1)。ArrayList在插入数据时还需要更新索引（除了插入数组的尾部）。

 - 类似于插入数据，删除数据时，LinkedList也优于ArrayList。

 - LinkedList需要更多的内存，因为ArrayList的每个索引的位置是实际的数据，而LinkedList中的每个节点中存储的是实际的数据和前后节点的位置。
 
####HashTable VS HashMap

 - HashMap几乎可以等价于Hashtable，除了HashMap是非synchronized的，并可以接受null(HashMap可以接受为null的键值(key)和值(value)，而Hashtable则不行)。
HashMap是非synchronized，而Hashtable是synchronized，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable；而如果没有正确的同步的话，多个线程是不能共享HashMap的。Java 5提供了ConcurrentHashMap，它是HashTable的替代，比HashTable的扩展性更好。
 - 另一个区别是HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM。这条同样也是Enumeration和Iterator的区别。
 - 由于Hashtable是线程安全的也是synchronized，所以在单线程环境下它比HashMap要慢。如果你不需要同步，只需要单一线程，那么使用HashMap性能要好过Hashtable。
HashMap不能保证随着时间的推移Map中的元素次序是不变的。


  [1]: http://segmentfault.com/img/bVmlh6