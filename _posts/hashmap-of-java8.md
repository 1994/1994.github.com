title: 初探Java8中的HashMap
date: 2015-07-24 12:20:20
categories: Develop
tags: [Java,HashMap,interview]
---

HashMap是我们最常用的集合之一，同时Java8也提升了HashMap的性能。本着学习的原则，在这探讨一下HashMap。
<!-- more --> 
###原理
简单讲解下HashMap的原理：HashMap基于Hash算法，我们通过put(key,value)存储，get(key)来获取。当传入key时，HashMap会根据key.hashCode()计算出hash值，根据hash值将value保存在bucket里。当计算出的hash值相同时怎么办呢，我们称之为Hash冲突，HashMap的做法是用链表和红黑树存储相同hash值的value。当Hash冲突的个数比较少时，使用链表，否则使用红黑树。

###数据结构

一图胜千言：

![](http://7xawrk.com1.z0.glb.clouddn.com/bloghashmapimg.png)

我们可以在HashMap的源码中找到这样一句：  

    transient Node<K,V>[] table;

很明显，HashMap还是凭借数组实现的，辅以链表和红黑树。我们知道数组的特点：寻址容易，插入和删除困难，而链表的特点是：寻址困难，插入和删除容易，红黑树则对插入时间、删除时间和查找时间提供了最好可能的最坏情况担保。HashpMap将这三者结合在一起。

###Hash算法

    static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
    
如果你也看过7之前的Hash算法，会发现这个版本的算法比之前的简洁。
###重要的内部类
####Node

	 static class Node<K,V> implements Map.Entry<K,V> {
	        final int hash;
	        final K key;
	        V value;
	        Node<K,V> next;
	
	        Node(int hash, K key, V value, Node<K,V> next) {
	            this.hash = hash;
	            this.key = key;
	            this.value = value;
	            this.next = next;
	        }
	
	        public final K getKey()        { return key; }
	        public final V getValue()      { return value; }
	        public final String toString() { return key + "=" + value; }
	
	        public final int hashCode() {
	            return Objects.hashCode(key) ^ Objects.hashCode(value);
	        }
	
	        public final V setValue(V newValue) {
	            V oldValue = value;
	            value = newValue;
	            return oldValue;
	        }
	
	        public final boolean equals(Object o) {
	            if (o == this)
	                return true;
	            if (o instanceof Map.Entry) {
	                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
	                if (Objects.equals(key, e.getKey()) &&
	                    Objects.equals(value, e.getValue()))
	                    return true;
	            }
	            return false;
	        }
	    }
链表节点，存储键值对，并含有一个next引用。

####TreeNode

	 static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
	        TreeNode<K,V> parent;  // red-black tree links
	        TreeNode<K,V> left;
	        TreeNode<K,V> right;
	        TreeNode<K,V> prev;    // needed to unlink next upon deletion
	        boolean red;
	        TreeNode(int hash, K key, V val, Node<K,V> next) {
	            super(hash, key, val, next);
	        }
	
	        /**
	         * Returns root of tree containing this node.
	         */
	        final TreeNode<K,V> root() {
	            for (TreeNode<K,V> r = this, p;;) {
	                if ((p = r.parent) == null)
	                    return r;
	                r = p;
	            }
	        }
	
	        /**
	         * Ensures that the given root is the first node of its bin.
	         */
	        static <K,V> void moveRootToFront(Node<K,V>[] tab, TreeNode<K,V> root) {
	            int n;
	            if (root != null && tab != null && (n = tab.length) > 0) {
	                int index = (n - 1) & root.hash;
	                TreeNode<K,V> first = (TreeNode<K,V>)tab[index];
	                if (root != first) {
	                    Node<K,V> rn;
	                    tab[index] = root;
	                    TreeNode<K,V> rp = root.prev;
	                    if ((rn = root.next) != null)
	                        ((TreeNode<K,V>)rn).prev = rp;
	                    if (rp != null)
	                        rp.next = rn;
	                    if (first != null)
	                        first.prev = root;
	                    root.next = first;
	                    root.prev = null;
	                }
	                assert checkInvariants(root);
	            }
	        }
红黑树的节点
###重要方法

	final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
	                   boolean evict) {
	        Node<K,V>[] tab; Node<K,V> p; int n, i;
	        if ((tab = table) == null || (n = tab.length) == 0)
	            n = (tab = resize()).length;
	        if ((p = tab[i = (n - 1) & hash]) == null)
	            tab[i] = newNode(hash, key, value, null);
	        else {
	            Node<K,V> e; K k;
	            if (p.hash == hash &&
	                ((k = p.key) == key || (key != null && key.equals(k))))
	                e = p;
	            else if (p instanceof TreeNode)
	                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
	            else {
	                for (int binCount = 0; ; ++binCount) {
	                    if ((e = p.next) == null) {
	                        p.next = newNode(hash, key, value, null);
	                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
	                            treeifyBin(tab, hash);
	                        break;
	                    }
	                    if (e.hash == hash &&
	                        ((k = e.key) == key || (key != null && key.equals(k))))
	                        break;
	                    p = e;
	                }
	            }
	            if (e != null) { // existing mapping for key
	                V oldValue = e.value;
	                if (!onlyIfAbsent || oldValue == null)
	                    e.value = value;
	                afterNodeAccess(e);
	                return oldValue;
	            }
	        }
	        ++modCount;
	        if (++size > threshold)
	            resize();
	        afterNodeInsertion(evict);
	        return null;
	    }

这是HashMap中的put函数，里面的参数boolean onlyIfAbsent,boolean evict我并不知道有什么用，因为put在调用的时候，是将这两个参数写死了，若知道请告知：

     public V put(K key, V value) {
    	return putVal(hash(key), key, value, false, true);
    }

另外我们可以看到，当节点个数>= TREEIFY_THRESHOLD - 1时，HashMap将采用红黑树存储。为什么这么做呢？正如我们前面提到的，当发生Hash冲突时，HashMap首先是采用链表将重复的值串起来，并将最后放入的值置于链首，java8对HashMap进行了优化。当节点个数多了之后使用红黑树存储。这样做的好处是，最坏的情况下即所有的key都Hash冲突，采用链表的话查找时间为O(n),而采用红黑树为O(logn)，这也是Java8中HashMap性能提升的奥秘，详细的测试可以看[这篇博文](http://it.deepinmind.com/%E6%80%A7%E8%83%BD/2014/04/24/hashmap-performance-in-java-8.html)。
###总结
这篇文章简单介绍了下Java8中的HashMap中的数据结构，Hash算法，内部类，简单分析了Java8中性能提升的奥秘，由于水平原因难免会出现一些纰漏，希望各位能即时纠正。

