排序也算是基本算法了。

# 一个简单的测试框架

```java
    static class SortTester {

        final List<AbstractSorter> sorters;

        SortTester(List<AbstractSorter> sorters) {
            this.sorters = sorters;
        }

        public void test() {
            List<int[]> arrays = getArrays();
            int[] res = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
            for (AbstractSorter sorter : sorters) {
                System.out.println("\n测试Sorter开始：" + sorter.getClass().getSimpleName());
                for (int[] array : arrays) {
                    sorter.sort(array);
                    assertArrayEquals(res, array);
                }
                System.out.println("测试Sorter结束：" + sorter.getClass().getSimpleName());
            }
        }

        private List<int[]> getArrays() {
            return Arrays.asList(
                    new int[]{1, 2, 3, 4, 5, 6, 7, 8, 9, 10},
                    new int[]{10, 9, 8, 7, 6, 5, 4, 3, 2, 1},
                    new int[]{10, 2, 5, 4, 7, 9, 8, 6, 1, 3},
                    new int[]{1, 2, 5, 8, 7, 9, 4, 6, 10, 3}
            );
        }
    }

    interface AbstractSorter {
        void sort(int[] a);

        default void swap(int[] a, int j, int i) {
            int t = a[j];
            a[j] = a[i];
            a[i] = t;
        }
    }
```

然后是各个排序算法的实现

# 冒泡排序

```java
    static class BubbleSort implements AbstractSorter {

        @Override
        public void sort(int[] a) {
            for (int i = 0; i < a.length - 1; i++) {
                for (int j = 0; j < a.length - i - 1; j++) {
                    if (a[j] > a[j + 1]) {
                        swap(a, j, j + 1);
                    }
                }
            }
        }
    }
```

# 选择排序

```java
    static class SelectSort implements AbstractSorter {

        @Override
        public void sort(int[] a) {
            for (int i = 0; i < a.length - 1; i++) {
                int minIdx = i;
                for (int j = i + 1; j < a.length; j++) {
                    if (a[minIdx] > a[j]) {
                        minIdx = j;
                    }
                }
                swap(a, minIdx, i);
            }
        }
    }
```

# 插入排序


# 希尔排序

# 快速排序


# 堆排序

# 桶排序


# 基数排序

# 计数排序




