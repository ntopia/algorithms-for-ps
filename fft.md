---
layout: page
title: Fast Fourier Transform
permalink: /fft
---

{{ page.title }}
================

두 다항식의 곱을 O(n^2) 보다 빠르게 계산하기 위해 사용한다. 좀 더 정확히는
[DFT](https://en.wikipedia.org/wiki/Discrete_Fourier_transform)를 O(nlogn) 에
구하고 그것을 이용해 O(n) 만에 두 다항식의 곱을 계산하게 된다.
여기서 DFT를 O(nlogn) 에 구하는 방법이 바로
[Fast Fourier Transform](https://en.wikipedia.org/wiki/Fast_Fourier_transform) 이다.

설명이 조금 더 필요함.

이 글의 내용은 대부분 [이 곳](http://e-maxx.ru/algo/fft_multiply) 에서 가져왔다.

구현
----

기본적인 구현

{% highlight cpp %}
typedef complex<double> base;

void fft(vector<base>& a, bool invert) {
  int n = (int)a.size();
  if (n == 1) {
    return;
  }

  vector<base> a0(n/2), a1(n/2);
  for (int i = 0, j = 0; i < n; i += 2, ++j) {
    a0[j] = a[i];
    a1[j] = a[i + 1];
  }
  fft(a0, invert);
  fft(a1, invert);

  double ang = 2*PI/n * (invert ? -1 : 1);
  base w(1), wn(cos(ang), sin(ang));
  for (int i = 0; i < n/2; ++i) {
    a[i] = a0[i] + w * a1[i];
    a[i + n/2] = a0[i] - w * a1[i];
    if (invert) {
      a[i] /= 2, a[i + n/2] /= 2;
    }
    w *= wn;
  }
}
{% endhighlight %}

이를 이용해 두 다항식을 곱하는 함수를 예제로 만들어보면 다음과 같다.

{% highlight cpp %}
void multiply(const vector<int>& a, const vector<int>& b, vector<int>& res) {
  vector<base> fa(a.begin(), a.end()), fb(b.begin(), b.end());
  size_t n = 1;
  while (n < max(a.size(), b.size())) {
    n <<= 1;
  }
  n <<= 1;
  fa.resize(n), fb.resize(n);

  fft(fa, false), fft(fb, false);
  for (size_t i = 0; i < n; ++i) {
    fa[i] *= fb[i];
  }
  fft(fa, true);

  res.resize(n);
  for (size_t i = 0; i < n; ++i) {
    res[i] = (int)(fa[i].real() + 0.5);
  }
}
{% endhighlight %}

적당히 최적화한 버전

{% highlight cpp %}
typedef complex<double> base;

void fft(vector<base>& a, bool invert) {
  int n = (int)a.size();

  for (int i = 1, j = 0; i < n; ++i) {
    int bit = n >> 1;
    for (; j >= bit; bit >>= 1) {
      j -= bit;
    }
    j += bit;
    if (i < j) {
      swap(a[i], a[j]);
    }
  }

  for (int len=2; len<=n; len<<=1) {
    double ang = 2*PI/len * (invert ? -1 : 1);
    base wlen(cos(ang), sin(ang));
    for (int i = 0; i < n; i += len) {
      base w(1);
      for (int j = 0; j < len/2; ++j) {
        base u = a[i + j], v = a[i + j + len/2] * w;
        a[i + j] = u + v;
        a[i + j + len/2] = u - v;
        w *= wlen;
      }
    }
  }
  if (invert) {
    for (int i = 0; i < n; ++i) {
      a[i] /= n;
    }
  }
}
{% endhighlight %}

