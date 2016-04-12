---
layout: page
title: Fermat primality test
category: Math
permalink: /math/fermat-test
use_math: true
---

{{ page.title }}
================

[위키백과 문서](https://en.wikipedia.org/wiki/Fermat_primality_test)

어떤 자연수가 소수인지 아닌지 빠르게 판정하는 확률적인 알고리즘이다.

페르마의 소정리 $ a^{p-1} \equiv 1 \pmod{p} $ 를 응용한 것이다.
$ 1 < a < p $ 인 $a$ 중에서 랜덤하게 하나를 고르고 위의 식을 만족하지 않는 지를 체크한다.
만족하지 않는다면 $p$는 합성수다. 만족한다면 처음부터 다시 반복한다.
적당한 반복 후에도 위의 식을 만족하지 않는 $a$를 찾지 못했다면 $p$는
매우 큰 확률로 소수이다.

이 알고리즘은 특정한 경우 꽤 높은 확률로 틀린 결과를 낼 수 있다.
$ 1 < b < n $ 이고 $ gcd(b, n) = 1 $인 모든 정수 $b$에 대해
$ b^{n-1}\equiv 1\pmod{n} $ 을 만족하는 합성수 $n$을
[Carmichael number](https://en.wikipedia.org/wiki/Carmichael_number) 라고 한다.
이런 수 $n$을 갖고 위의 알고리즘을 수행하여 제대로 된 결과를 내려면
$ gcd(a, n) \neq 1 $ 이 될 때까지 $a$를 골라야 하는데 이럴 확률이 꽤 낮다.
따라서 입력으로 Carmichael number가 주어질 때, 충분한 반복횟수가 보장되지
않는다면 틀린 결과를 낼 확률이 높아진다.



정보
----

* 시간복잡도 : 반복횟수를 $k$라 할 때, $ O(k\log{n}) $


구현
----

{% highlight cpp %}
typedef long long ll;

ll modpow(ll a, ll n, ll mod) {
  ll ret = 1;
  while (n) {
    if (n % 2)
      ret = (ret * a) % mod;
    a = (a * a) % mod;
    n /= 2;
  }
  return ret;
}

bool isPrime(ll n) {
  if (n <= 1 || (n > 2 && n % 2 == 0))
    return false;
  if (n <= 3)
    return true;

  mt19937 gen;
  uniform_int_distribution<ll> dis(2, n - 2);
  for (int k = 0; k < 10; ++k) {
    ll pick = dis(gen);
    if (modpow(pick, n - 1, n) != 1)
      return false;
  }
  return true;
}
{% endhighlight %}
