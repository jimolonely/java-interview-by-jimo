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

```java
    static class InsertSort implements AbstractSorter {

        @Override
        public void sort(int[] a) {
            for (int i = 1; i < a.length; i++) {
                for (int j = i; j > 0; j--) {
                    if (a[j - 1] > a[j]) {
                        swap(a, j - 1, j);
                    } else {
                        break;
                    }
                }
            }
        }
    }
```

# 希尔排序

# 快速排序

```java
    static class QuickSort implements AbstractSorter {

        @Override
        public void sort(int[] a) {
            quickSort(a, 0, a.length - 1);
        }

        private void quickSort(int[] a, int left, int right) {
            if (left < right) {
                int mid = move(a, left, right);
                quickSort(a, left, mid - 1);
                quickSort(a, mid + 1, right);
            }
        }

        private int move(int[] a, int start, int end) {
            // 用中间的数作为划分值，避免有序情况
            int mid = start + (end - start) / 2;
            swap(a, mid, start);
            int pivot = a[start];
            int left = start;
            int right = end;
            while (left < right) {
                // 每一轮
                // 从右边找到一个比sentinel小的
                while (left < right && a[right] >= pivot) {
                    right--;
                }
                if (left < right) {
                    a[left] = a[right];
                    left++;
                }
                // 再从左边找到一个比sentinel大的
                while (left < right && a[left] <= pivot) {
                    left++;
                }
                if (left < right) {
                    a[right] = a[left];
                    right--;
                }
            }
            // 再给枢纽位置赋值
            a[left] = pivot;
            return left;
        }
    }
```

# 堆排序



# 桶排序


# 基数排序

# 计数排序




