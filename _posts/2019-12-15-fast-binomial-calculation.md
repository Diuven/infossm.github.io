---
layout: post
title:  "이항계수의 빠른 계산"
author: ho94949
date: 2019-12-14 15:00
tags: [factorial, Stirling-number]
---

# 서론

 $\binom{N}{K}$ 는 $N$개의 구분이 되는 물체 중 $K$개를 고르는 가짓수이고, 이를 이항계수라고 부른다.  이항계수는 한 조합론적 상황에서 많이 사용된다. 이 $\binom{N}{K} = \dfrac{N!}{K!(N-K)!}$임이 잘 알려져 있다. 이 수를  소수가 아닌 임의의 수 $M$으로 나눈 나머지를 구하는 방법에 대해서 알아본다.

# Naive

이항계수를 $M$으로 나눈 나머지를 구하는 가장 쉬운 방법은 실제로 $N!$을 그냥 1부터 $N$까지의 모든 수를 곱하는 것으로 계산하는 것이다. 하지만 이럴 경우에 나눗셈이 잘 되지 않는다.

예를 들어서, $M=9$인 경우에는, $6! = 720$과 $7!=5040$을 9로 나눈 나머지는 모두 0이다. 이 둘을 단순히 $M$으로 나눈 나머지만 저장한다면, $\dfrac{7!}{6!} = 7$과, $\dfrac{6!}{6!} = 1$을 구분할 수 없을 것이다. 그래서 우리는 다음의 방법을 사용하려고 한다.

그래서 임의의 크기 정수 곱셈을 사용하거나, 다음의 방법을 사용해야한다.

## CRT

중국인의 나머지 정리는 $x \equiv a_i \pmod {b_i}$ 꼴의 조건이 여럿 있을 때, 이를 모두 만족하는 $x$를 찾는 알고리즘이다.

다음과 같은 두 식을 모두 만족하는 $x$에 대해 생각하자. (여기서, $b_1$과 $b_2$는 편의상 서로소라고 하자.)

- $ x \equiv b_2 \pmod {b_1}$
- $ x \equiv 0 \pmod{b_2}$

이를 만족하는 $x$ 중 하나는 $b_2$라는 것을 쉽게 알 수 있고, $b_1b_2$ 로 나눈 나머지와 $b_1$, $b_2$로 나눈 나머지는 일대일 대응이 되는 것을 쉽게 보일 수 있기 때문에, $x \equiv b_2 \pmod{b_1b_2}$ 라는 사실을 알 수 있다.

여기서 $b_2^{-1}$을 $b_2 b_2^{-1} \equiv 1 \pmod{b_1}$인 $b_2$의 역원이라고 하면 

- $ b_2^{-1}x \equiv b_2^{-1}b_2 \equiv 1 \pmod {b_1}$
- $ b_2^{-1}x \equiv 0 \pmod{b_2}$

라는 사실을 알 수 있다. 그럼 다음과 같은 식 

- $ y \equiv 1 \pmod {b_1}$
- $ y \equiv 0 \pmod {b_2}$

이 있을 때, $y \equiv b_2^{-1}x \equiv b_2 b_2^{-1} \pmod{b_1b_2}$ 이라는 사실을 알 수 있다. 마찬가지로, 

- $ z \equiv 0 \pmod {b_1}$
- $ z \equiv 1 \pmod {b_2}$

를 만족하는 $z$는, $b_1^{-1}$을 $b_1 b_1^{-1} \equiv 1 \pmod{b_2}$인 $b_1$의 역원이라고 하면 $z \equiv b_1 b_1^{-1} \pmod{b_1b_2}$이라는 사실을 알 수 있다.

이제, 

- $ x \equiv a_1 \pmod {b_1}$
- $ x \equiv a_2 \pmod {b_2}$

와 같은 식은, $x = a_1y +a_2z \equiv a_1b_2 b_2^{-1} + a_2b_1 b_1^{-1}  \pmod{b_1b_2}$ 라는 것을 바로 알 수 있다.



여기서 중국인의 나머지 정리를 사용하는 이유는, 우리가 나머지 $M$을 소인수 분해 한 후, 각 소인수에 대해서 $n!$을 $p^e$로 나눈 나머지를 구한 후 중국인의 나머지 정리를 사용하면 문제를 쉽게 해결할 수 있을 것이기 때문이다.

## $p^e$ 꼴의 역원


$\dfrac{A}{B} \bmod M$을 구하기 위해서, $A$와 $B$를 $(xp^e+y)p^z$에서 $(y, z)$를 저장할 것이다. 여기서 $y$는 0 초과 $p^e$이하의 $p$와 서로소인 수이다.
그럼 $y$의 $p^e$에 대한 역원을 확장 유클리드 호제법을 사용하여 찾을 수 있기 때문에, 앞 부분의 계산을 할 수 있고, $p^z$를 $p^w$로 나누면 몫이 $p^{z-w}$이기 때문에, 뒷 부분도 모두 계산할 수 있다.

그래서 우리는 $N! \pmod {p^e}$를 $(xp^e+y)p^z$로 표현한 형태에서 $(y, z)$를 구할 수 있어야 한다. 여기서 $e=1$인 경우를 다음 [N! mod P의 빠른 계산](http://www.secmem.org/blog/2019/09/17/fast-factorial-calculation/) 포스트에서 다루었다. 이제, $e\ge2$ 인 일반적인 경우에서도 문제를 해결하여 보자. 이 경우에 우리가 같은 방법을 사용하지 못하는 이유는 FFT를 하는 과정에서 일반적인 $p^e$ 꼴의 나머지를 쉽게 다룰 수 없기 때문이다.


# Attacks 

우리는 $(n!)_p$ 을 1이상 $n$이하의 수 중에서 $p$의 배수가 아닌 수들을 모두 곱한 수를 $p^e$로 나눈 나머지로 정의할 것이다.

이 경우에, $n! \equiv (n!)_p \left(\left\lfloor\dfrac{n}{p}\right\rfloor!\right)_p p^{\left\lfloor\frac{n}{p}\right\rfloor}  \left(\left\lfloor\dfrac{n}{p^2}\right\rfloor!\right)_p p^{\left\lfloor\frac{n}{p^2}\right\rfloor} \cdots \pmod{p^e}$ 와 같은 형태로 표현 할 수 있기 때문에, 단순한 곱셈과 덧셈으로 $(xp^e+y)p^z$ 형태를 계산해 줄 수 있다. 이를 위해서 알아야 할 것들이 몇 가지 있다.

(부호 없는) 제 1종 스털링 수는 $\genfrac{[}{]}{0pt}{1}{a}{b}$로 표현되며, 다음과 같은 식을 만족하는 수이다.

$x(x+1) \cdots (x+n-1) = \sum_{k=0}^{n} \genfrac{[}{]}{0pt}{1}{n}{k} x^k$ 

이를 계산하기 위한 점화식 $\genfrac{[}{]}{0pt}{1}{n+1}{k} = n \genfrac{[}{]}{0pt}{1}{n}{k} + \genfrac{[}{]}{0pt}{1}{n}{k-1}$은 어렵지 않게 얻어낼 수 있으며, 동적 계획법을 사용하여 쉽게 계산할 수 있다.

이제 $n = up+v$이고 $v$가 0 이상 $p$미만이라고 할 때 $(n!)_p$ 를 계산 해 보자.

$(n!)_p$ = $((up+v)!)_p$

$ = $ $\left(\prod_{i=0}^{u-1}\prod_{j=1}^{p-1}(ip+j) \right) \cdot \prod_{j=1}^{v}(up+j)$

$\equiv$ $\left(\prod_{i=0}^{u-1}\left(\sum_{k=0}^{e-1} (ip)^k\genfrac{[}{]}{0pt}{1}{p}{k+1} \right)\right) \left( \sum_{k=0}^{e-1} (up)^k \genfrac{[}{]}{0pt}{1}{v+1}{k+1} \right) $


$\equiv$ $\genfrac{[}{]}{0pt}{1}{p}{1}^u \left(\prod_{i=0}^{u-1}\left(1+\sum_{k=1}^{e-1} \dfrac{\genfrac{[}{]}{0pt}{1}{p}{k+1}}{\genfrac{[}{]}{0pt}{1}{p}{1}} (ip)^k \right)\right) \left( \sum_{k=0}^{e-1} (up)^k \genfrac{[}{]}{0pt}{1}{v+1}{k+1} \right) \pmod {p^e}$

이다.

여기서 우리는 놀랍게 $f_{p, e}(u) = \prod_{i=0}^{u-1}\left(1+\sum_{k=1}^{e-1} \dfrac{\genfrac{[}{]}{0pt}{1}{p}{k+1}}{\genfrac{[}{]}{0pt}{1}{p}{1}} (ip)^k \right)$ 부분이 $2e-2$차의 다항식이 되고, 그렇기 때문에, $f_{p, e}(0), \cdots, f_{p, e}(2e-2)$ 만 계산 해 두면, $O(e \log p)$ 시간에 라그랑주 보간으로 $f_{p, e}(u)$의 값을 구할 수 있다.

## $f_{p, e}(u)$의 다항식 증명

임의의 $p$와 $e$에 대해서, $a_k \in \mathbb{Q}$ 이고, $a_k$의 분모가 $p$와 서로소라고 하자. 이 때,

$g(x, u) = \prod_{i=0}^{u-1} \left(1 + \sum_{k=1}^{e-1}a_k(xi)^k\right)$ 

라고 하면

$\log(g(x, u)) = \sum_{i=0}^{u-1} \log \left(1 + \sum_{k=1}^{e-1} a_k (xi)^k \right)$

으로 표현할 수 있고, $\log(1+x)$를 맥클로린 급수 전개의 각 항이 유리수라는 것을 이용하면, $s_k(x) = \sum_{i=0}^{x-1} i^k$인 $k+1$차 다항식이라고 할 때, 다음이 성립한다.

$\log(g(x, u)) = \sum_{i=0}^{u-1} \sum_{k=1}^{\infty} b_k x^k i^k = \sum_{k=1}^{\infty} b_k x^k s_k(u)$ 

인 적당한 $b_k \in \mathbb{Q}$가 존재한다.

다시 $g(x, u)$ 를 구하면 

$g(x, u) = \exp\left(\sum_{k=1}^{\infty} b_k x^k s_k (u) \right) = 1 + \sum_{k = 1}^ {\infty} t_k (u) \cdot x^k$ 

가 된다.

역시 같은 방법으로 맥클로린 급수 전개를 사용하면, $t_k(x)$는 $2k$차 이하의 다항식이 된다.

$g(x, u)$에 $x=p$를 대입하고 $p^e$로 나눈 나머지를 확인 한 경우에, $x^e$ 이후의 항은 없어지므로

$g(p, u) \equiv 1 + \sum_{k=1}^{e-1} t_k(u) \times p^k \pmod{p^e}$ 가 되며, $2e-2$차 다항식이 된다.


# 구현

실제로 구현하는 부분은, 제 1종 스털링수를 구하는데 $O(pe)$의 시간복잡도를 사용하고, $f_{p, e}$ 다항식 보간을 하는데에 또 $O(pe)$의 시간복잡도를 사용하고 필요한 $(n!)_p$ 를 계산 하는 데에 $O(\log_p n \cdot e \log p) = O(e \log n)$ 의 시간이 들기 때문에, 총 $O(pe + e\log n)$의 시간복잡도로 $\binom{n}{m} \pmod{p^e}$ 를 계산할 수 있다.