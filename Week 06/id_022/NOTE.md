# 第六周 学习总结

## Word Search II时间复杂度分析
1. 维护字典树：O(N*k), N为单词数量，k为单词平均长度
2. 搜索：O(m*n*4^k), m*n矩阵行数和列数，每个位置向4个方向查询
总体时间复杂度：O(N*k + m*n*4^k)

## 并查集代码模版
```python
def init(p): 
	# for i = 0 .. n: p[i] = i; 
	p = [i for i in range(n)] 
 
def union(self, p, i, j): 
	p1 = self.parent(p, i) 
	p2 = self.parent(p, j) 
	p[p1] = p2 
 
def parent(self, p, i): 
	root = i 
	while p[root] != root: 
		root = p[root] 
	while p[i] != i: # 路径压缩 ?
		x = i; i = p[i]; p[x] = root 
	return root
```

## 双向BFS代码模版
```python
def bfs(graph, start, end):

	begin_queue, end_queue = [], [] 
	begin_queue.append(start)
    end_queue.append(end)

	while begin_queue and end_queue:
        if len(begin_queue) > len(end_queue):
            begin_queue, end_queue = end_queue, begin_queue

        node = begin_queue.pop() 
		visited.add(node)

		process(node) 
		nodes = generate_related_nodes(node)

        # terminator
        if (is_begin_meet_end(nodes, end)):
            return

		begin_queue.push(nodes)

	# other processing work
```

## A*搜索代码模版
```python
def AstarSearch(graph, start, end):

	pq = collections.priority_queue() # 优先级 —> 估价函数
	pq.append([start]) 
	visited.add(start)

	while pq: 
		node = pq.pop() # can we add more intelligence here ?
		visited.add(node)

		process(node) 
		nodes = generate_related_nodes(node) 
   unvisited = [node for node in nodes if node not in visited]
		pq.push(unvisited)
```
