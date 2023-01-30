---
title: CPP 1. Type of expressions, references, and move semantics.
layout: article
tags: series-cpp cpp computer-science
---

안녕하세요. 이번 시리즈에서는 C++에 관한 이것저것을 다루고자 합니다. 대부분의 경우 C++17 (gcc 기준)을 타겟팅하여 다룰 예정입니다. 거의 모든 시리즈가 1~2편밖에 안 나오는 것 같네요.. 사실 Linear Algebra 2편을 만들다가 회사에서의 일들, 이직 등 때문에 많은 플로우가 끊겼는데, 나머지 시리즈들도 시간이 남을 때 만들어보도록 하겠습니다.

이 시리즈 또한 기초적인 내용(for, if문 사용법 등등..)은 제외하고, 제가 다루고 싶은 주제들을 하나하나 다루어보고자 합니다. 이번 포스팅에서는 C++의 expression type에 관하여 소개하고자 합니다.

---

## History

C언어와 C++98에서는 expression의 종류를 크게 "lvalue"와 "rvalue"라는 2가지로 나눴습니다. lvalue는 left value로 등호 왼쪽에 오는 값이고, rvalue는 right value로 등호 오른쪽에 오는 값이라는 아주 심플한 컨셉을 가지고 있었습니다. 하지만 C++11부터 복합 카테고리를 포함한 총 5가지 컨셉의 expression type이 완성되면서 직관적인 이해가 조금 더 어려운 방향으로 진화하였습니다.

## 5 Expression Types

다음은 [Microsoft C++ Tutorial](https://learn.microsoft.com/en-us/cpp/cpp/lvalues-and-rvalues-visual-cpp?view=msvc-170)에서 만든 Expression Type Classification 이미지입니다.

![img1](https://learn.microsoft.com/en-us/cpp/cpp/media/value_categories.png?view=msvc-170)

[dydtjr1128님의 블로그](https://dydtjr1128.github.io/cpp/2019/06/10/Cpp-values.html)에서 좋은 이미지가 있어서 이것도 첨부합니다.

![img2](https://dydtjr1128.github.io/img/Cpp/values2.png)

이 표를 기반으로 하나하나 따져보도록 하겠습니다. 그리고 그것들을 따져보기 전, 다음 2가지를 잠깐 짚고 넘어가도록 하겠습니다.

### Have identity vs. Have no identity

Identity를 가진다는 것은, 해당 변수가 그 자체만으로도 식별 가능하다는 것을 의미합니다. 더 자세하게 파면, 서로 다른 두 expression이 온전히 "같은" 값을 가리키고 있는 지 판별하는 것이 가능한 값들을 의미합니다.

### Movable vs Unmovable

어떤 expression이 movable하다는 것은, move constructor, move assignment operator, 혹은 move semantics를 구현하는 어떤 함수가 이 expression에 적용 가능한 지를 말합니다. 메모리 상에서 값이 이동 가능한 지를 묻는 것과는 다릅니다!

---

## glvalue (Generalized Left Value)

> glvalue has an identity.

glvalue의 특징은 다음과 같습니다.

- glvalue는 lvalue -> rvalue, array -> pointer, function -> pointer 등의 implicit conversion에 의해 prvalue로 변환될 수 있습니다.
- glvalue는 [polymorphic](https://en.cppreference.com/w/cpp/language/object#Polymorphic_objects)할 수 있습니다.
- glvalue는 [incomplete type](https://en.cppreference.com/w/cpp/language/type#Incomplete_type)을 가질 수 있습니다.

## rvalue (Right Value)

> rvalue can be moved.

rvalue의 특징은 다음과 같습니다.

- rvalue의 주소는 프로그래머가 직접 알아낼 수 없습니다. (오직 컴파일러만 접근 가능합니다.) 예를 들어 `&1`, `&i++`, `&std::move(x)` 는 모두 컴파일 불가합니다.
- rvalue는 built-in 대입연산자(`=`)의 왼쪽에 위치할 수 없습니다.
- rvalue는 "const lvalue reference" 또는 "rvalue reference"가 만들어질 때 쓰일 수 있으며, 그렇게 될 경우 해당 오브젝트의 수명이 더 길어지게 됩니다. (해당 reference가 끝날 때까지로 연장)
- rvalue가 2개의 overloaded signature(하나는 const lvalue reference, 다른 하나는 rvalue reference를 받는)가 있는 함수의 인자로 들어갔을 때, rvalue reference를 받는 함수가 호출이 됩니다.

## lvalue (Left Value)

> lvalue has an identity, but cannot be moved.

lvalue는 glvalue의 자식이므로, glvalue의 특징을 모두 물려받습니다. 그 외에, 다음과 같은 특징이 있습니다.

- lvalue의 주소는 `&` 연산자를 통해 접근할 수 있습니다. (다른 값들은 불가능)
- const가 아닌 lvalue는 대입연산자 부류의 왼쪽 피연산자가 될 수 있습니다.
- lvalue는 lvalue reference를 만드는 데 쓸 수 있습니다.

lvalue로 가능한 값들은 다음 목록과 같습니다.

- 변수, 함수의 이름
- 데이터 멤버(타입 무관): `std::cin` 등
- lvalue reference를 return하는 함수 콜: `std::getline(std::cin, x)`, `std::cout << 1` 등
- 대입연산 부류: `a = b`, `a += b` 등
- 빌트인 **prefix** 증감 연산: `a++`, `a--`
- 빌트인 참조 연산: `*ptr`
- 빌트인 subscription 연산: `array[n]`
- 오브젝트 멤버/멤버참조 연산 (단, 예외 몇 가지 있음): `obj.member`, `objptr->member`
- 오브젝트/포인터의 멤버를 향하는 포인터 연산: `obj.*alias`, `objptr->*alias`
- 콤마 연산(단, 콤마 오른쪽 값이 lvalue여야 함): `a, b`
- 삼항 연산(b, c 값이 특정 조건들 중 하나를 만족하면 되는데 이 리스트가 굉장히 복잡합니다.. 보통 둘 다 lvalue인 상황이 흔합니다): `a ? b : c`
- 문자열 리터럴: `"abcdefg"`
- lvalue reference를 향한 casting: `static_cast<int&>(x)` 등
- lvalue reference type의 non-type 템플릿 파라미터(`template <int n>` 같은 걸 얘기하는 듯 합니다)
- 리턴 타입이 "rvalue reference to function"인 함수 콜(또는 오버로딩된 연산자의 연산)
- "rvalue reference to function"을 향한 casting: `static_cast<void (&&)(int)>(x)`

## prvalue (Pure Right Value)

> prvalue has no identity, but can be moved.

prvalue는 rvalue의 자식이므로, rvalue의 특징을 모두 물려받습니다. 그 외에, 다음과 같은 특징이 있습니다.

- prvalue는 polymorphic할 수 없습니다. (glvalue와 반대)
- non-class non-array prvalue는 (해당 값이 materialized되지 않은 한) const/volatile이 붙을 수 없습니다.
- prvalue는 incomplete type을 가질 수 없습니다. (glvalue와 반대)
- prvalue를 가지는 Abstract class type이나 배열은 존재할 수 없습니다.

prvalue로 가능한 값들은 다음과 같습니다.

- 문자열을 제외한 모든 리터럴: `1`, `-2.34`, `true`, `nullptr` 등
- non-reference를 반환하는 모든 함수 콜이나 오버로딩된 연산자 연산: `int f(int x)`의 `f(10)`, `str1 + str2` 등
- **Postfix** 증감 연산: `a++`, `a--`
- 빌트인 arithmetic, logical, comparison 연산: `a + b`, `a || b`, `a << b`, `a <= b` 등
- 빌트인 주소 참조 연산: `&ptr`
- 포인터/오브젝트의 멤버/멤버포인터 참조 연산: `obj.member`, `ptr->member`, `obj.*memberptr`, `ptr->*memberptr` (non-static member에 한함)
- 콤마 연산(`b`가 rvalue): `a, b`
- 삼항 연산(이것 역시 `b`, `c`가 특정 조건 하에..): `a ? b : c`
- 클래스의 `this` 포인터
- Enum의 [enumerator](https://en.cppreference.com/w/cpp/language/enum)
- non-type scalar 템플릿 파라미터
- 람다 익스프레션: `[](int x){ return x+1; }` 등

## xvalue (eXpiring Value)

> xvalue has an identity, and also can be moved.

xvalue는 glvalue의 자식이면서 동시에 rvalue의 자식이므로, glvalue의 특징과 rvalue의 특징을 모두 갖습니다.

xvalue로 가능한 값들은 다음과 같습니다.

- 오브젝트의 멤버 / 멤버 포인터 연산 (단, `obj`가 rvalue이고 `member`가 non-static member일 때): `obj.member`, `obj.*member`
- 삼항 연산(b, c 값이 특정 조건들 중 하나를 만족할 때): `a ? b : c`
- 리턴 타입이 rvalue reference인 함수 콜 또는 오버로딩된 연산자의 연산: `std::move(x)` 등
- array rvalue에 대한 빌트인 subscription 연산: `array[n]`
- rvalue reference type을 향한 cast 연산: `static_cast<char&&>(x)` 등
- [Temporary materialization](https://en.cppreference.com/w/cpp/language/implicit_conversion#Temporary_materialization) 이후에 발생하는 모든 임시 오브젝트

---

C++