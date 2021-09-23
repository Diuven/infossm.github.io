---
layout: post
title:  "와일드카드 문자열 매칭"
author: ho94949
date: 2021-08-20 15:00
tags: [string, FFT]
---

# 서론
문자열 매칭 알고리즘은, 문자열 $S$와 $T$가 주어졌을 때, $S$의 부분문자열(substring) 중에 $T$가 어느 위치에 등장하는지 찾는데 사용하는 알고리즘이다. 우리는 이 문자열 매칭 알고리즘에 와일드카드 문자, 즉, 어떤 문자에도 대응 될 수 있는 `?` 문자가 들어갔을 때 어떤 식으로 문제를 해결해야 하는지 알아본다.

`?` 문자가 들어가지 않았을 때 문자열 매칭을 하는 법에 대해서는, [KMP 알고리즘과 Aho-Corasick 알고리즘](https://www.secmem.org/blog/2021/01/25/KMP-and-Aho-Corasick/) 문서에 잘 설명이 되어 있으니, 참고하면 된다.

# 문제
우리가 풀고 싶은 문제는 다음과 같다.

- 문자열 길이 $N$의 문자열 $S$와, 길이 $M$의 문자열 $T$가 주어진다. 여기서 $i$ 번째 ($0 \le i \le N-M$) 문자로 시작하는 $S$의 부분문자열 $S[i, i+M)$가 $T$와 매칭되는 모든 $i$를 찾아라. 

여기서 문자열 $S[i, i+M)$가 $T$와 매칭된다는 의미는 다음과 같다.

- $0 \le j < M$인 모든 $j$에 대해, $S_{i+j} = T_j$이거나, $S_{i+j} = ?$이거나, $T_j = ?$이다.

## naïve
$S[i, i+M)$과 $T$가 매칭되는지 여부는 $O(M)$ 시간에 판단 가능하니, 총 $O(NM)$ 시간 안에 문자열 매칭이 일어나는 모든 $i$를 찾을 수 있다.

## 문자열 매칭의 다항식 표현
일반적인  (`?`가 없는) 문자열 매칭을 다항식으로 표현해 보자. 문자열 $S$와 $T$의 각 문자를 수로 표현했을 때, $S_{i+j}$와 $T_j$가 같다는 것을, $S_{i+j}-T_j = 0$으로 표현할 수 있다.

여기서 우리가 와일드카드 문자인 `?`를 $0$으로 표현할 경우, $S_{i+j}$ 와 $T_j$ 가 동일하거나, 둘 중 하나가 `?`라는 것을 $S_{i+j}T_j(S_{i+j}-T_j) = 0$과 같이 표현할 수 있고, 이 값이 $0$ 혹은 양수가 되도록 하기 위해서는, 모든 `?`가 아닌 문자를 양수로 표현하고, $(S_{i+j} - T_j)$라는 식에 제곱을 씌워, 항상 음이 아니게 되도록 할 수 있다. 이후 모든 $j$에 대해 식을 더하면, 해당 식은 $0$이거나 양수이고, $0$인 경우는 식의 모든 값이 $0$인 경우만 해당된다.

즉, $S[i, i+M)$과 $T$의 매칭 여부를  $\sum_{j=0}^{M-1}S_{i+j}T_j(S_{i+j}-T_j)^2 = 0$와 같이 표현할 수 있다. 

## 다항식 표현의 계산
$\sum_{j=0}^{M-1}S_{i+j}T_j(S_{i+j}-T_j)^2 = \sum_{j=0}^{M-1} (S_{i+j}^3T_j - 2S_{i+j}^2 T_j^2 + S_{i+j}T_j^3)$ 이고 $T$를 거꾸로 쓴 문자열 $T^R$에 대해, $T^R_i = T_{M-1-i}$이라는 점을 이용하면 식을 $\sum_{j=0}^{M-1} \left(S_{i+j}^3T^R_{M-1-j} - 2S_{i+j}^2 {T^R_{M-1-j}}^2 + S_{i+j}{T^R_{M-1-j}}^3\right)$으로 바꿀 수 있다. 이는 $S^k$와 ${T^R}^{4-k}$를 $k=1, 2, 3$에 대해 합성곱을 계산 한 이후, 각각 $1$, $-2$, $1$의 계수와 함께 더해서 $M-1+i$ 번째 값을 확인하는 것으로 계산할 수 있다. 이 값이 $0$인 경우, $i$ 번째 위치에서 매칭이 일어나고, 아닌 경우 매칭이 일어나지 않는다. 이 방법으로 $O(N \log N)$ 시간에 문제를 해결할 수 있다.