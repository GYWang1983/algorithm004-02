# 第二周 学习总结

## 1. HashMap 源代码分析
```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {

    //Map中存储键值对的数据结构
    //Node中存储的是对于的Value和一个链表（next）
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
        // ...
    }

    //实际存储数据的数组
    transient Node<K,V>[] table;

    //Map.get(key)实现方法，参数为Key的hash值和Key对象本身
    //根据hash值查找数组的对应位置的Node对象，
    //然后比较查询的key对象是否和Node.key对象相等（先比引用，不想等在equals比较）
    //如果相等则返回数组中的Node对象
    //如果key不想等则遍历Node.next链表中是否有和key相等的Node
    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    //搜索二叉树
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }

    //Map.get(key)实现方法
    //根据hash值对应的数组位置没有元素，创建一个新的Node放入数组
    //否则比较数组对应位置的Key是否等于参数key，如果相等则替换当前Node的值
    //否则搜索链表（或二叉树），如果找到则替换对应Node，如果没找到则创建新的Node加入链表或二叉树
    //然后比较查询的key对象是否和Node.key对象相等（先比引用，不想等在equals比较）
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

    //当数组内元素数量达到阈值后需要重新分配数组空间
    final Node<K,V>[] resize() {
        //...
    }

    //Map容量不是非常小的时候，将Node的链表转化为红黑树
    final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            TreeNode<K,V> hd = null, tl = null;
            do {
                TreeNode<K,V> p = replacementTreeNode(e, null);
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }

    // Java8当Map较大时是用红黑树代替链表
    static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // red-black tree links
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red;
        //....
    }

}
```
---

## 2. 学习总结
* 算法应用
    * 如果能够找到最近重复子问题，使用递归解决
    * 排列组合问题，可以转化为状态树的遍历
    * N皇后问题，尽早对状态树剪枝
    * 字母异位词等字符串分群问题，可以寻找一个合适的Hash算法
    * **N皇后问题中的斜线是否被占用的判断，可以使用解析几何点斜式表示**

* 代码模版
    * 通用递归
    ```java
    1. if (终止条件) return
    2. 处理当前层
    3. 调用下一层
    4. 清理当前层状态
    ```

    * 回溯
    ```java
    1. if (终止条件) return
    2. 处理当前层
    3. 调用下一层
    4. 回退当前层
    ```

    * 分治
    ```java
    1. if (终止条件) return
    2. 处理当前层
    3. 调用下一层
    4. 组装下层返回结构
    5. 清理当前层状态
    ```


