# 第一周 学习总结

## 1. 改写Deque代码

```java
Deque<String> deque = new LinkedList<>();

deque.addFirst("a");
deque.addFirst("b");
deque.addFirst("c");
System.out.println(deque);

String str = deque.peekFirst();
System.out.println(str);
System.out.println(deque);

while(deque.size() > 0) {
    System.out.println(deque.removeFirst());
}

System.out.println(deque);
```
---

## 2. PriorityQueue 源代码分析
```java
public class PriorityQueue<E> extends AbstractQueue<E>
    implements java.io.Serializable {

    // 实际为数组存储的平衡二叉树，堆顶为最小元素
    transient Object[] queue;

    // 元素数量
    int size;

    // 优先级比较器
    private final Comparator<? super E> comparator;

    // 入队, 时间复杂度为O(logn)
    public boolean offer(E e) {
        if (e == null)
            throw new NullPointerException();
        modCount++;
        int i = size;
        if (i >= queue.length)
            grow(i + 1);
        
        // 在队尾插入元素e，实际操作为最小堆的
        // 最小堆插入，插入到叶子节点，如果叶子节点比父节点小，则交换父子位置，直到插入的节点比父节点大为止
        // 时间复杂度为O(logn)
        siftUp(i, e); 
        size = i + 1;
        return true;
    }

    private static <T> void siftUpComparable(int k, T x, Object[] es) {
        Comparable<? super T> key = (Comparable<? super T>) x;
        while (k > 0) {
            int parent = (k - 1) >>> 1;
            Object e = es[parent];
            if (key.compareTo((T) e) >= 0)
                break;
            es[k] = e;
            k = parent;
        }
        es[k] = key;
    }

    // 出队，出队队列中的最小元素，时间复杂度O(logn)
    public E poll() {
        final Object[] es;
        final E result;

        if ((result = (E) ((es = queue)[0])) != null) {
            modCount++;
            final int n;
            final E x = (E) es[(n = --size)];
            es[n] = null;
            if (n > 0) {

                // 删除堆顶元素
                // 将二叉树最后一个元素移动至堆顶，调整二叉树。
                // 时间复杂度O(logn)
                final Comparator<? super E> cmp;
                if ((cmp = comparator) == null)
                    siftDownComparable(0, x, es, n);
                else
                    siftDownUsingComparator(0, x, es, n, cmp);
            }
        }
        return result;
    }

    private static <T> void siftDownComparable(int k, T x, Object[] es, int n) {
        // assert n > 0;
        Comparable<? super T> key = (Comparable<? super T>)x;
        int half = n >>> 1;           // loop while a non-leaf
        while (k < half) {
            int child = (k << 1) + 1; // assume left child is least
            Object c = es[child];
            int right = child + 1;
            if (right < n &&
                ((Comparable<? super T>) c).compareTo((T) es[right]) > 0)
                c = es[child = right];
            if (key.compareTo((T) c) <= 0)
                break;
            es[k] = c;
            k = child;
        }
        es[k] = key;
    }
```
---

## 3. 学习总结
* 各数据结构操作的时间复杂度比较

|  |prepend|append|lookup|insert|delete
|---|---|---|---|---|---|
|Array|***O(1)?***|O(1)|O(1)|O(n)|O(n)|
|LinkedList|O(1)|O(1)|O(n)|O(1)|O(1)|
|SkipList|O(1)|O(1)|O(logn)|O(1)|O(1)|
|Stack||O(1)|O(n)||O(1)|
|Queue||O(1)|O(n)||O(1)|
|Deque|O(1)|O(1)|O(n)||O(1)|
|PriorityQueue(Heap)||***O(1)?***|O(logn)||O(logn)|

* 问题：
    1. 数组的prepend为什么是O(1)?
    1. 优先队列如何同时做到入队是O(1)，出队是O(logn)?

* 算法应用
    * 查询对象的左右边界问题，可以考虑双指针或有序栈
    * 滑动时间窗问题，使用双端队列


  

