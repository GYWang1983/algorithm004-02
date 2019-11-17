# 第三周 学习总结

## 1. 使用二分查找，寻找一个半有序数组中间无序的地方
例如: [4, 5, 6, 7, 0, 1, 2]

思路: 
1. 对于半有序数组是滚动产生的，因此在这个数组中的任意片段，如果左边界的值小于又边界的值，说明左边是有序的，否则右侧是有序的
2. 将左右边界移动至无序的部分，继续二分查找无序的部分
3. 直到左右边界相等时即找到无序的部分
```java
public int search2(int[] nums) {
    if (nums == null || nums.length == 0) {
        return -1;
    }

    int l = 0, r = nums.length - 1;
    while (l < r - 1) {
        int mid = (l + r) / 2;
        if (nums[l] < nums[mid]) {
            l = mid;
        } else {
            r = mid;
        }
    }

    return nums[l] < nums[r] ? l : r;
}
```

## 2. 学习总结

* 代码模版
    * 深度优先搜索（DFS）
    ```python
    visited = set() 
    def dfs(node, visited):
        if node in visited: # terminator
	        # already visited 
	        return 

	    visited.add(node) 

	    # process current node here. 
	    ...
	    for next_node in node.children(): 
		    if not next_node in visited: 
			    dfs(next_node, visited)
    ```

    * 广度优先搜索（BFS）
    ```python
    def bfs(graph, start, end):
	    queue = []
        visited = []
	    queue.append(start) 
	    
	    while queue: 
		    node = queue.pop() 
		    visited.add(node)

		    process(node)
		    nodes = generate_related_nodes(node)
            for n in nodes
                if not n in visited: 
		            queue.push(n)

	        # other processing work 
    ```

    * 二分查找代码模版
    ```python
    left, right = 0, len(array) - 1 
    while left <= right: 
	  mid = (left + right) / 2 
	  if array[mid] == target: 
		    # find the target!! 
		    break or return result 
	  elif array[mid] < target: 
		    left = mid + 1 
	  else: 
		    right = mid - 1
    ```

* 算法应用
    * 子问题最优解能得到全局最优解，可以使用贪心算法
    * 二分查找前提条件: 单调性、有界性、可以索引访问
    * Floodfill类的问题可以使用广度优先搜索
    * 按层次遍历可使用广度优先搜索
    * 有序序列搜索可使用二分查找
