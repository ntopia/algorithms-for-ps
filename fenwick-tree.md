---
layout: page
title: Fenwick tree
---

{{ page.title }}
================

[위키백과 문서](https://en.wikipedia.org/wiki/Fenwick_tree)

prefix value를 빠르게 구할 때 쓸 수 있는 트리 자료구조이다. 이를 이용해 특정한
연속된 구간에 있는 원소들의 합도 빠르게 구할 수 있다. 단, prefix value만
가져올 수 있으므로 최대값이나 최소값을 빠르게 구해야 할 때에는 쓸 수 없다.
구현이 간단해서 자주 쓰인다.


정보
----

* 공간 복잡도 : O(n)
* 시간 복잡도
  * 한 점 업데이트 : O(logn)
  * prefix sum 계산 : O(logn)


구현
----

{% highlight cpp %}
int tree[TREE_SIZE];

// Returns the sum from index 1 to pos
int query(int pos) {
  int sum = 0;
  while (pos > 0) {
    sum += tree[pos];
    pos -= pos & -pos;
  }
  return sum;
}

// Adds val to element with index pos
void add(int pos, int val) {
  while (pos < TREE_SIZE) {
    tree[pos] += val;
    pos += pos & -pos;
  }
}
{% endhighlight %}


테스트 문제
-----------

coming soon..
