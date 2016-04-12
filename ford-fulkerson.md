---
layout: page
title: Ford-Fulkerson algorithm
category: Graph
subcategory: Maximum Flow
permalink: /math/ford-fulkerson
use_math: true
---

{{ page.title }}
================

[위키백과 문서](https://en.wikipedia.org/wiki/Ford%E2%80%93Fulkerson_algorithm)

maximum flow를 구하는 가장 기본적인 알고리즘. 증가경로를 찾을 때 dfs를 이용한다.

증가경로를 찾을 때 bfs를 사용할 수도 있는데, 이러면 증가경로 중 거리가 가장 짧은
것을 찾게 된다. 이런 것을 특별히 [Edmonds-Karp algorithm](https://en.wikipedia.org/wiki/Edmonds%E2%80%93Karp_algorithm)
이라 하고, 시간복잡도가 $ O(V^{2}E) $ 가 된다. 두 알고리즘은 분명 다르지만
여기서는 그냥 한 문서에서 다 다루고 있다.

일반적으로는 bfs가 좀 더 나은 성능을 보여준다. 하지만 정점, 간선은 엄청 많은데
소스와 싱크 사이의 거리가 대단히 짧은 경우엔 (예: 이분매칭) dfs를 사용하는 것이
좀 더 효율적이다.



정보
----

* 시간복잡도 : $ O(Ef) $ ($f$는 maximum flow의 값)
  * bfs를 사용한 Edmonds-Karp algorithm 의 경우 : $ O(V^{2}E) $


구현
----

dfs를 사용한 기본적인 포드 폴커슨 구현

{% highlight cpp %}
struct MaxFlow {
  typedef int flow_t;
  struct Edge {
    int next, inv;
    flow_t cap, res;
  };

  int n;
  vector<vector<Edge>> graph;
  vector<int> visit;
  int vtime;

  void init(int _n) {
    n = _n;
    vtime = 0;
    graph.resize(n);
    for (int i = 0; i < n; i++)
      graph[i].clear();
    visit.resize(n);
    memset(&visit[0], 0, sizeof(visit[0]) * visit.size());
  }
  void addEdge(int s, int e, flow_t cap, flow_t caprev = 0) {
    Edge forward{ e, graph[e].size(), cap, cap };
    Edge reverse{ s, graph[s].size(), caprev, caprev };
    graph[s].push_back(forward);
    graph[e].push_back(reverse);
  }
  void clearFlow() {
    for (int i = 0; i < n; ++i) {
      for (auto& edge : graph[i])
        edge.res = edge.cap;
    }
  }

  flow_t dfs(int u, int ed, int curflow) {
    visit[u] = vtime;
    if (u == ed)
      return curflow;

    for (auto& edge : graph[u]) {
      if (edge.res <= 0 || visit[edge.next] == vtime)
        continue;
      auto ret = dfs(edge.next, ed, min(curflow, edge.res));
      if (ret > 0) {
        edge.res -= ret;
        graph[edge.next][edge.inv].res += ret;
        return ret;
      }
    }
    return 0;
  }

  flow_t solve(int source, int sink) {
    flow_t ans = 0;
    while (true) {
      ++vtime;
      auto flow = dfs(source, sink, numeric_limits<flow_t>::max());
      if (flow == 0)
        break;
      ans += flow;
    }
    return ans;
  }
};
{% endhighlight %}


관련글
------

* [Maximum Flow: Section 1](https://www.topcoder.com/community/data-science/data-science-tutorials/maximum-flow-section-1/)
* [Maximum Flow: Section 2](https://www.topcoder.com/community/data-science/data-science-tutorials/maximum-flow-section-2/)
