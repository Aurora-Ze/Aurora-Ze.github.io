---
title: String、StringBuffer
categories: 基础
mathjax: true
---

# String

最近在做算法题时用到了StringBuffer的一些方法

所以特地来了解一下String类与StringBuffer类的一些原理及操作

## 不可变性

在学Java之初，我们就已经知道String是不可变的

> 什么是不可变呢？
>
> 例如对一个String类型的对象进行修改时，每次得到的都是新的String对象
>
> 而原来的String对象是是一直不变的

但它究竟是怎么实现的呢

我们先来看下String的源码，可以看到

- **final关键字修饰了类和字符数组**
  - 禁止继承，避免子类继承后，修改内容
- **字符数组还是private的**
  - 一定程度上防止外界访问到字符数组

```Java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
```

但是只靠这样，就能保证String的不可变性吗

## final关键字

**首先，我们可以知道一点，用final关键字修饰的变量，一旦被创建就无法再修改**

所以下面将`tmp`的引用赋值给`arr`，会提示错误

```java 
    final int[] arr = new int[]{1,2,3};
    int[] tmp = new int[]{4,5,6};
    arr = tmp;
```

但是arr数组在内存中其实是分为两部分的，即**引用**和**数组本体**

**引用放在Stack中，而数组本体在Heap中**

如果我们直接去修改堆中的数组，是否会造成改变呢

```Java
	final int[] arr = new int[]{1,2,3};
	arr[0] = 10;
	// 输出 10，2，3
	// 可以final数组改变了
```

由此我们知道用`final`关键字修饰，还是无法避免数组内容的修改

## replace()源码分析

那么该怎样避免这种情况呢，我们很容易就能想到，当然是依赖于具体方法的实现。

就以`replace(oldChar, newChar)`为例，我们先来分析下大致逻辑

- 在$oldChar != newChar$时
- 然后在原数组中找到第一个oldChar
- 如果存在oldChar
  - 创建新数组
  - oldChar之前的字符直接复制到新数组
  - oldChar及以后的字符，边复制边判断
  - 如果等于oldChar，就替换成newChar
- 如果$oldChar==newChar$或者原数组中没有oldChar，就直接返回原数组

```Java
public String replace(char oldChar, char newChar) {
    if (oldChar != newChar) {
        int len = value.length;
        int i = -1;
        char[] val = value; /* avoid getfield opcode */

        while (++i < len) {
            if (val[i] == oldChar) {
                break;
            }
        }
        if (i < len) {
            char buf[] = new char[len];
            for (int j = 0; j < i; j++) {
                buf[j] = val[j];
            }
            while (i < len) {
                char c = val[i];
                buf[i] = (c == oldChar) ? newChar : c;
                i++;
            }
            return new String(buf, true);
        }
    }
    return this;
}
```

看完逻辑之后，我们发现当存在oldChar时，他会创建一个新的数组，替换oldChar并返回

也许在这之前，我们会好奇它为什么要创建新的数组

**但今天我们很容易就联想到，它的这个操作正是为了防止原数组被修改，从而保证了String的不可变性**

## 不可变的作用

从上面我们大概可以推断出，String类的设计者在类的方法中尽力避免原数组的修改，来保证其不可变性

那不可变究竟有什么用呢？

- 最重要的一点，就是安全
  - String不可变，可以避免将String对象作为传参时，被用户不小心修改的情况
  - 在并发场景下，因为String的不可写（写了之后变成新对象，没有意义），所以是线程安全的

## 总结

String的各种方法都避免对原数组进行修改，这才是String不可变性的主要实现方式

String类用final关键字修饰

- 防止子类继承，去破坏它的不可变性
- String类本身已经很完美了，无需其他类继承它

# StringBuffer

当需要修改字符串内容时，我们一般采用`StringBuffer`类

既然如此，就来看下StringBuffer的实现吧

## StringBuffer结构

**类结构如下**

![](/images/sb_1.PNG)

可以看到，StringBuffer类中有一个字符数组`toStringCache`，注释表明

> 该数组存放的是由最近一次toString()方法返回的值
>
> 并且当StringBuffer发生变化时，它会被清空

```Java
public final class StringBuffer
   extends AbstractStringBuilder
   implements java.io.Serializable, CharSequence { 
     /**
     * A cache of the last value returned by toString. Cleared
     * whenever the StringBuffer is modified.
     */
    private transient char[] toStringCache;
}
```

## append(String)

**再来看看常用的append方法**

**正如上面所说，在StringBuffer添加字符串时，会把toStringCache清空。**清空的原因在看到toString()时再讲

而且它调用了父类的append方法进行添加字符串

```Java
@Override
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```

下面是`AbstractStringBuilder`这个抽象类提供的append方法

主要逻辑如下：

- 判断传入的String是否为空
  - 为空，调用`appendNull()`
  - 非空，确保数组容量
  - 再把字符串添加到字符数组中

```Java
public AbstractStringBuilder append(String str) {
    if (str == null)
        return appendNull();
    int len = str.length();
    ensureCapacityInternal(count + len);
    str.getChars(0, len, value, count);
    count += len;
    return this;
```

上面主要用到了三个方法`appendNull()`、`ensureCapacityInternal()`和`getChars()`

下面依次分析

## ensureCapacityInternal(int)

为什么要先分析这个方法呢，因为appendNull()里面也用到了。。

如果我们之前看过`ArrayList`相关的源码，对这类方法应该不陌生了吧

- 方法形参为**minimumCapacity**
  - 从上面append中，可以发现实参是$count+len$
  - 即插入`str`后的数组长度，暂时把它叫做**最小容量**吧
- 方法内部就是把最小容量与数组当前的长度进行比较
  - 如果$minimumCapacity > value.length$
  - 则要进行扩容，调用Arrays.copyOf()，这个方法可太熟了，就不再赘述

```Java
private void ensureCapacityInternal(int minimumCapacity) {
    // overflow-conscious code
    if (minimumCapacity - value.length > 0) {
        value = Arrays.copyOf(value,
                newCapacity(minimumCapacity));
    }
}
```

不会以为到这儿就结束了吧，我们还有一点没讲，就是扩容的大小选择

那就来看看**newCapacity(int)**方法

```java
// 回顾下参数：
// minCapacity: 插入元素后的数组长度，即最小容量
// newCapacity: 原数组长度 * 2 + 2，即新容量
private int newCapacity(int minCapacity) {
   	// 如果新容量小于最小容量，就把新容量设置成最小容量
    int newCapacity = (value.length << 1) + 2;
    if (newCapacity - minCapacity < 0) {
        newCapacity = minCapacity;
    }
    // 经过上述判断后的新容量
    // 如果小于0（整型溢出） 或 大于最大数组容量（定义的Integer.MAX_VALUE - 8）
    // 就让新容量为最大数组容量
    // 否则就不变，直接返回
    return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
        ? hugeCapacity(minCapacity)
        : newCapacity;
}

private int hugeCapacity(int minCapacity) {
    if (Integer.MAX_VALUE - minCapacity < 0) { // overflow
        throw new OutOfMemoryError();
    }
    return (minCapacity > MAX_ARRAY_SIZE)
        ? minCapacity : MAX_ARRAY_SIZE;
}
```

## appendNull()

如果append(String)传入null，就会进入这个方法

方法内只做了两件事

1. 判断要不要扩容
2. 在数组中添加“null”字符串

```Java
private AbstractStringBuilder appendNull() {
    int c = count;
    ensureCapacityInternal(c + 4);
    final char[] value = this.value;
    value[c++] = 'n';
    value[c++] = 'u';
    value[c++] = 'l';
    value[c++] = 'l';
    count = c;
    return this;
}
```

## getChars()

该方法是由**String类**提供的

忽略那些判断，主要是调用**System.arraycopy()把调用方法的String对象的部分字符拷贝到目的数组中**

```java
public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin) {
    if (srcBegin < 0) {
        throw new StringIndexOutOfBoundsException(srcBegin);
    }
    if (srcEnd > value.length) {
        throw new StringIndexOutOfBoundsException(srcEnd);
    }
    if (srcBegin > srcEnd) {
        throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
    }
    System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
}
```

## toString()

分析完StringBuffer的append()后，我们再来看下它如何返回得到字符串

主要是把`Arrays.copyOfRange`返回的字符数组，赋值给toStringCache

然后再通过String类构造器创建一个String对象

```Java
@Override
public synchronized String toString() {
    if (toStringCache == null) {
        toStringCache = Arrays.copyOfRange(value, 0, count);
    }
    return new String(toStringCache, true);
}
```

**copyOfRange()**

我们发现内部是创建了新数组来存放元素

```java
public static char[] copyOfRange(char[] original, int from, int to) {
    int newLength = to - from;
    if (newLength < 0)
        throw new IllegalArgumentException(from + " > " + to);
    char[] copy = new char[newLength];
    System.arraycopy(original, from, copy, 0,
                     Math.min(original.length - from, newLength));
    return copy;
}
```

然后我们解释下append方法中为什么要把toStringCache设置为null

> 1.当StringBuffer对象发生变化时，通过调用toString会得到一个新的toStringCache，保证了旧的一个不会再发生变化
>
> 2.当StringBuffer对象没有发生变化时，多次调用toString不会产生重复的字符串

