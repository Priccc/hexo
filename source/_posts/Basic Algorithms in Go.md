---
title: Basic Algorithms in Go
tags: [algorithm,golang]
categories: [ACM]
grammar_cjkRuby: true
---
最近学Go，感觉挺不错的。闲来无事用它写了几种常用的基础算法。
## 快排
思想很简单，实现起来为了方便每次以left作为基准，也可以使用BFS来节省递归栈：
```go
// QuickSort returns a sorted slice
func QuickSort(arr []int) {
	if len(arr) <= 1 {
		return
	}
	left, right := 0, len(arr)-1
	for left < right {
		if arr[left+1] > arr[left] {
			arr[left+1], arr[right] = arr[right], arr[left+1]
			right--
		} else {
			arr[left+1], arr[left] = arr[left], arr[left+1]
			left++
		}
	}
	QuickSort(arr[:left])
	QuickSort(arr[left+1:])
}
```
## 最短路
最短路核心思想就是Relax操作。效率高的单源最短路有下面两种算法：
1. Dijikstra，不能处理负权路,但是时间复杂度比较稳定.
2. SPFA是我比较喜欢的一种算法，可以判断负权路。正常情况的时间复杂度为`O(kE)`  其中`k<<V`；最好的情况即一次BFS，时间复杂度为 `O(E)`，然而对于某些精心构造的图，复杂度可以达到Bellman-ford级别：`O(VE)`。
下面构图使用的是邻接表（适用于稀疏图），也可以用邻接矩阵（适用于稠密图）。
```go
// adjacency list definition
type edge struct {
	v, w int
	next *edge
}

type Graph struct {
	head []*edge
	n, m int
}

func (G *Graph) init(n, m int) {
	G.n, G.m = n, m
	G.head = make([]*edge, n)
}

func (G *Graph) add(u, v, w int) {
	G.head[u] = &edge{
		v:    v,
		w:    w,
		next: G.head[u],
	}
}

// SPFA returns the shortest path from begin to end
// begin: the begin node number
// end: the end node number
func (G *Graph) SPFA(begin, end int) int {
	visit := make([]bool, G.n)
	dist := make([]int, G.n)
	Q := make([]int, 0)
	for i := 0; i < G.n; i++ {
		dist[i] = -1
	}
	dist[begin] = 0
	Q = append(Q, begin)
	visit[begin] = true
	for len(Q) != 0 {
		u := Q[0]
		for e := G.head[u]; e != nil; e = e.next {
			if dist[e.v] == -1 || dist[e.v] > dist[u]+e.w {
				dist[e.v] = dist[u] + e.w
				if !visit[e.v] {
					Q = append(Q, e.v)
					visit[e.v] = true
				}
			}
		}
		// pop the front node
		Q = Q[1:]
		visit[u] = false
	}
	return dist[end]
}
```



___To Be Continue...___