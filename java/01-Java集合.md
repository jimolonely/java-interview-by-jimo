## 集合类
盗个图

![java-collection](./images/01-java-colllection.png)

主要分为Map和Collection接口，Collection下有List、Set和Queue。Map下有HashMap、TreeMap和LinkedHashMap。

## 为什么LinkedHashMap能保持插入顺序？
因为其内部实现为双向链表.

## EnumMap何时使用？

如果`map`的`key`是枚举类型，推荐使用`EnumMap`，因为其内部结构紧凑，用数组存储枚举`key`，不需要计算`hashcode`就能通过索引定位key，更加高效和节省空间。

## Queue的常见操作有哪些？有什么区别？

无非增加、删除和查看，文档注释里已经很明确，就是抛异常和返回`null`的区别。

![queue](./images/01-queue.png)

`add`在队列满时会抛出`IllegalStateException`，例如`AbstractQueue`里的实现：

```java
    public boolean add(E e) {
        if (offer(e))
            return true;
        else
            throw new IllegalStateException("Queue full");
    }
```

但也不是所有队列实现都这样，`PriorityQueue`就和`offer`实现一样。

```java
    public boolean add(E e) {
        return offer(e);
    }
```

## 阻塞队列是什么？有哪些阻塞队列？

阻塞队列也是队列，比普通队列多了2个操作：阻塞获取和阻塞插入数据，分别对应方法：

`put`,`take` ,`offer(time)`, `poll(time)`

常见的阻塞队列如下：

![block-queue](./images/01-blobk-queue.png)

## 每种阻塞队列有什么区别，在什么时候使用？

TODO

