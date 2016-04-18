---
layout: page
title: Manacher's algorithm
category: String
permalink: /string/manachers-algorithm
use_math: true
---

{{ page.title }}
================

[위키백과 문서](https://en.wikipedia.org/wiki/Longest_palindromic_substring#Manacher.27s_algorithm)

문자열이 주어지면 그 안에 있는 팰린드롬들을 빠르게 찾는 알고리즘이다.
구체적으로는 문자열의 각 문자 $S[i]$에 대해 그 문자를 중심으로 하는
가장 긴 팰린드롬의 지름 $A[i]$를 계산한다.

짝수 길이의 팰린드롬을 찾고 싶으면 각 문자 사이에 '#' 같은 더미 문자를 넣거나
애초에 구현할 때 문자 사이의 빈공간을 문자로 보는 식으로 구현하면 된다.


정보
----

* 시간복잡도 : $ O(n) $


구현
----

plen 배열이 팰린드롬의 지름을 저장하는 배열이다
{% highlight cpp %}
int r = -1, p = -1;
for (int i = 0; i < n; ++i) {
  if (i <= r)
    plen[i] = min((2 * p - i >= 0) ? plen[2 * p - i] : 0, r - i);
  else
    plen[i] = 0;
  while (i - plen[i] - 1 >= 0 && i + plen[i] + 1 < n
         && str[i - plen[i] - 1] == str[i + plen[i] + 1]) {
    plen[i] += 1;
  }
  if (i + plen[i] > r) {
    r = i + plen[i];
    p = i;
  }
}
{% endhighlight %}


관련글
------

* [알고스팟 위키](https://algospot.com/wiki/read/Manacher%27s_algorithm)
