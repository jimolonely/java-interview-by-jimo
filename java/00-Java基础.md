## equals(),hashcode(),clone()几个方法的区别？
阅读hashcode的注释可知：

Returns a hash code value for the object.This method is supported for the benefit of hash tables such as those provided by java.util.HashMap.
The general contract of hashCode is:
* Whenever it is invoked on the same object more than once during an execution of a Java application, the hashCode method must consistently return the same integer, provided no information used in equals comparisons on the object is modified. This integer need not remain consistent from one execution of an application to another execution of the same application.
* If two objects are equal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce the same integer result.
* It is not required that if two objects are unequal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce distinct integer results. However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.


**equals相同的对象，hashcode必须相同，但反过来不一定。**

equals具有4个特性：

* It is reflexive: for any non-null reference value x, x.equals(x) should return true.
* It is symmetric: for any non-null reference values x and y, x.equals(y) should return true if and only if y.equals(x) returns true.
* It is transitive: for any non-null reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.
* It is consistent: for any non-null reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the objects is modified.
* For any non-null reference value x, x.equals(null) should return false.

## hashcode在什么时候用到？

hashmap里：putVal时会同时计算hashcode和equals

```java
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

## Java如何反转字符串？

StringReader和StringBuffer的reverse方法。

## 什么是检查异常？

根本定义是非`RuntimeException`的子类就是检查异常，对应运行时异常。

## Number类型的 i=7和j=7, i==j吗？

等于，Number类型是所有基本数字类型的抽象父类。

下面代码 i和j其实是Integer对象，满足 128（不包括128）以内共用实例的条件。
```java
Number i = 7;
Number j = 7;
System.out.println(i == j);
```
关于这里自动装箱的源码分析，会使用 `Integer.valueOf`

```java
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```
关于这个 `IntegerCache`源码很简单：会默认将 `-128`到`127`的Integer实例缓存到数组。

这个最大值可以通过JVM参数`-XX:AutoBoxCacheMax=<size>`配置
```java
    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }
```




