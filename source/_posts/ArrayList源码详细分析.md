---
title: ArrayList源码详细分析
date: 2019-06-26 23:55:10
tags:
---
先看一下接口实现:
```
//接口实现表明ArrayList支持克隆,随机访问,序列化
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```
再看构造函数:
```
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
private static final Object[] EMPTY_ELEMENTDATA = {};
//默认初始容量为10
private static final int DEFAULT_CAPACITY = 10;
//记录集合的元素个数,调用size()方法返回该值
private int size;
//底层采用Object数组存储
transient Object[] elementData;
//默认构造函数
public ArrayList() {
    //创建空数组
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
//构造包含collection元素的数组，这些元素使用迭代器的方式返回
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        //如果collection中的元素个数为0，创建空数组
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
//用户指定初始容量
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        //创建initialCapacity大小的数组
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        //初始容量为0，创建空数组
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        //如果初始容量小于0,则抛出异常
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
        }
    }
```
继续看`add()`方法
```
public boolean add(E e) {
    //在添加元素之前先调用ensureCapacityInternal()方法
    ensureCapacityInternal(size + 1);
    //可以看到相当于对数组赋值
    elementData[size++] = e;
    return true;
}
```
继续分析`ensureCapacityInternal()`方法
```
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}
//用于计算最小的数组大小
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    //这里看到当使用默认构造函数时，初始化容量为10
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}
private void ensureExplicitCapacity(int minCapacity) {
    //用于标记修改次数，通常在多线程抛出ConcurrentModificationException异常时就是通过modeCount来判断
    modCount++;
    if (minCapacity - elementData.length > 0)
        //如果最小容量比当前数组的容量大,则调用grow()方法扩容
        grow(minCapacity);
}
```
继续分析`grow()`方法:
```
private void grow(int minCapacity) {
    //把当前的数组容量赋值给oldCapacity
    int oldCapacity = elementData.length;
    //oldCapacity左移1位，相当于做oldCapacity/2，新的容量为1.5*oldCapacity
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
    //当没有初始容量或者初始容量为1
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    //把旧的数组内容拷贝到新的数组中
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```
可以看到，在`grow()`方法中完成扩容，这是`add`方法的过程，再来看在指定位置增加元素过程：
```
public void add(int index, E element) {
    //检查边界，判断index是否在0~size之间
    rangeCheckForAdd(index);
    ensureCapacityInternal(size + 1);
    //在拷贝的时候只把index~size之间的内容拷贝到新的数组中，前面的内容不变，相当于index之后的元素全部后移一位
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;
    size++;
}
private void rangeCheckForAdd(int index) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```
其他的`add`操作类似，不再详述。再看一下`remove`方法：
```
public E remove(int index) {
        //边界检查，由于对象都是连续存放，只要判断index是否大于list的size即可
        rangeCheck(index);
        modCount++;
        E oldValue = elementData(index);
        //ArrayList删除元素需要移动数组，确保
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //GC
        elementData[--size] = null;

        return oldValue;
}
private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```