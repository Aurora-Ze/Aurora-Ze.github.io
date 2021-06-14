---
title: HashMap
categories: 基础
tags:
	- 源码
---

## 1. hash算法

即扰动函数

**key** 的哈希码的高16位与低16位进行**异或**，增加低十六位的**随机性**

```Java
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```



## 2. 计算数组下标

> 简单地说，下标就是 hash 除以 数组容量，得到的**余数**
>
> 具体计算采用的是**与运算**

n表示数组大小，初始值为**16**

在n是2的整数幂情况下，n - 1的二进制低位全是1，高位全是0

这样 n - 1与哈希值进行**与**运算时，就只保留了hash的低位，来作为数组下标访问

~~因此Java规定该数组大小必须是2的整数次幂~~，是通过tableSizeFor方法实现的

16 - 1 的二进制有个特点，即**低位全是1，高位全是0**



这样做明显会出现碰撞的可能，所以就需要**上述1**中的扰动函数，来增加低位hash值的随机性

```python
tab[i = (n - 1) & hash]
```



## 3. 扩容机制（resize）

- **threshold：**临界值

- **capacity：**数组容量，默认为16

- **load factor：**负载因子，默认为0.75

**计算公式：**$\quad threshold = capacity * load factor$

当HashMap中的元素个数大于临界值时，就会进行扩容：

1. 把数组容量变为原来的两倍
2. 重新计算hash(这里是指元素在散列表中的位置，即索引，与数组大小有关，所以发生了变化)



## 4. 插入元素

插入元素时：

1. 如果数组索引处节点是空的，就新建一个节点
2. 如果索引处节点不为空，则插入到链表中（1.7头插法，1.8尾插法）
3. 如果是树节点，则调用插入树节点的方法

插入完毕后：

如果链表长度大于8，则判断数组容量，小于64就进行**resize**，大于64就**由链表**优化成**红黑树**

##### 5.tableSizeFor方法

传入用户指定的初始容量，然后返回大于该数值的最近的2幂次，例如输入11，返回16（2的四次）

```java
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```



## 5. putVal方法

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0) // 判断数组是否为空或长度为0，然后进行扩容
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null) // 索引命中的地方为空，则直接插入到数组中
            tab[i] = newNode(hash, key, value, null); // 是在方法最后面判断size+1后容量是否超过上限
        else {
            // 数组索引处存在节点，发生碰撞
            Node<K,V> e; K k;
            if (p.hash == hash &&  // 判断是不是重复元素，如果是的话，会在下面覆盖。
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
            if (e != null) { // 覆盖重复元素
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue; // 返回旧的value，覆盖相同key的时候才会从这里返回
            }					 // 这里还可以让HashSet的add方法返回false
        }						 // 因为add方法调用了putVal方法，具体返回为 putVal(...) == null
        ++modCount;				 // false则说明在HashMap中遇到并覆盖了重复的元素
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

HashSet的add方法

```java
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
    // 这里可以看到，HashSet添加元素，是把元素作为map的key,而map的value则是一个常量Object，在最上方定义。
}
```