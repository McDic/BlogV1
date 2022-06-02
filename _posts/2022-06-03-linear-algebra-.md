---
title: LA 1. 
layout: article
tags: series-la math linear-algebra
---

안녕하세요. 반년만의 포스팅이네요.. 제가 1~2달 전쯤부터 온라인으로 수학 스터디를 시작했는데, 스스로 정리를 해가면서 진행해야 할 것 같아서.. 포스팅을 시작하게 되었습니다. 이번 시리즈에서는 Linear Algebra(이하 **LA**)를 다루고자 합니다. 교재는 Stephen H. Friedberg 외 2명이 작성한 [Linear Algebra 5th Edition](https://www.amazon.com/Linear-Algebra-5th-Stephen-Friedberg/dp/0134860241)을 참고했습니다.

---

# Chapter 1. Vector Spaces

**Field**: *Field $F$ is a set on which two operations $+$, $\cdot$ are defined so that, for $\forall_{x, y}$ there are unique elements $x+y, x \cdot y$ and following conditions hold;*

- Commutativity(교환법칙) of $+, \cdot$: $a+b=b+a$, $a \cdot b = b \cdot a$
- Associativity(결합법칙) of $+, \cdot$: $(a+b)+c=a+(b+c)$, $(a \cdot b) \cdot c = a \cdot (b \cdot c)$
- Existence of identity elements(항등원 존재) for $+, \cdot$: $0+a = a$, $1 \cdot a = a$
- Existence of inverses(역원) for $+, \cdot$: $\forall_{a} \exists_{a'} a+a'=0$, $\forall_{b} \exists_{b'} b \cdot b' = 1$ ($b \ne 0$)
- Distributivity(분배법칙) of $\cdot$ over $+$: $a \cdot (b+c) = a \cdot b + a \cdot c$

Field의 예시를 들면...

- Set of real numbers of typical addition and multiplication
- Boolean set $\{True, False\}$ where $+$ is "or" and $\cdot$ is "and"

**Vector Space (Linear Space)**: *Vector Space $V$ over a field $F$ with operations $+, \cdot$ (addition and scalar multiplication) are defined so that, $\forall_{x, y \in V}, \forall_{a \in F}$ there are unique elements $x+y$ and $ax$, and following conditions hold;*

- **VS 1** / Commutativity(교환법칙) of $+$: $\forall_{x, y \in V} x+y=y+x$
- **VS 2** / Associativity(결합법칙) of $+$: $\forall_{x, y, z \in V} (x+y)+z = x+(y+z)$
- **VS 3** / Existence of identity elements(항등원) for $+$: $\forall_{x \in V} \exists_{0} x+0 = x$
- **VS 4** / Existence of inverse(역원) for $+$: $\forall_{x \in V} \exists_{x'} x+x'=0$
- **VS 5** / Existence of identity elements(항등원) for $\cdot$: $\forall_{x \in V} 1x = x$
- **VS 6** / Associativity(결합법칙) of $\cdot$: $\forall_{a, b \in F} \forall_{x \in V} (ab)x = a(bx)$
- **VS 7** / Distributivity(분배법칙) of $\cdot$ over $+$ (1): $\forall_{a \in F} \forall_{x, y \in V} a(x+y) = ax+ay$
- **VS 8** / Distributivity(분배법칙) of $\cdot$ over $+$ (2): $\forall_{a, b \in F} \forall_{x \in V} (a+b)x = ax+bx$

$V$의 원소를 **vector**라 부르고, $F$의 원소를 **scalar**라 부르기로 정의합니다.

Vector Space의 예를 들자면..

- Field $F$로부터 만들어지는 모든 $n$-tuple들의 집합을 $F^n$이라 표현합니다. 이때, $F^n$은 Vector Space입니다. ex: $R^3$, $C^2$ 등
- $m \times n$ 크기를 가지는 모든 행렬(**matrix**)들의 집합 또한 Vector Space입니다. 행렬들끼리 곱하면 크기가 달라질 수도 있어서 Vector Space가 아니지 않나? 싶을 수 있는데, **VS** 내용에는 Vector Space 원소들 간의 곱셈에 대한 언급이 없습니다.
- $S$가 공집합이 아닌 어떤 집합이고, $F$가 어떤 Field일 때, $f(S, F)$를 $S$에서 $F$로 가는 임의의 함수들의 집합이라고 합시다. $f = g \iff \forall_{s \in S} f(s) = g(s)$ 라고 할 때, $f(S, F)$는 다음 addition과 scalar multiplication에 대한 Vector Space입니다. 

    $$(f+g)(s) = f(s)+g(s), (cf)(s) = c(f(s))$$

- Field $F$로부터 **coefficient**(계수)를 뽑아낸 **Polynomial**(다항식)은 다음과 같이 정의됩니다.

    $$f(x) = \sum_{i=0}^{n} a_i x^i$$

    - Polynomial's **degree**(차수): Polynomial의 계수가 0이 아닌 가장 높은 항의 차수를 의미합니다. 만약 모든 항의 계수가 0일 경우, 이 polynomial은 **zero polynomial**이라고 하며, 차수를 편의상 $-1$로 취급합니다.

    Polynomial의 addition을 계수끼리만 더하는 것으로 생각하고, scalar multiplication을 모든 계수에 똑같은 상수를 곱해주는 것으로 생각하면, 그리고 두 polynomial의 equality를 모든 계수가 동일해야 같다는 것으로 생각하면,, 모든 Polynomial들의 집합 또는 Vector Space입니다. ($n$-tuple과 거의 동일하다고 생각하면 될 것 같습니다.)

- **Sequence**(수열): Sequence in $F$ is a function $\sigma$