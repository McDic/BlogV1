---
title: CPP 2. Access Specifiers and Friend
layout: article
tags: series-cpp cpp computer-science
key: cpp-2
---

안녕하세요. C++ 시리즈의 2번째 게시글입니다! 이번에는 컴파일러가 클래스의 멤버에 대한 접근 권한을 다루는 키워드들, **access specifiers**와 **friend**에 관하여 다루어보고자 합니다.

---

## Motivation

코드/프로젝트가 커지면 커질수록, 경우에 따라 특정 범위의 코드에서는 다른 어떤 변수나 함수, 타입 등에 접근하지 못하게 막고 싶을 수도 있습니다.
C++에서는 비교적 심플하게 그 문제를 해결할 수 있게 해주는 **access specifier**라는 개념이 존재합니다. 지금부터 차례차례 살펴봅시다.

---

## Access Specifiers

다음과 같은 C++ 코드가 있습니다.

```cpp
#include <iostream>
#include <string>

struct employee {
    int salary;
    std::string name;
};

int main(void) {
    employee blog_author(123, "wow");
    std::cout << blog_author.name << std::endl;
    std::cout << blog_author.salary << std::endl;
}
```

`main` 함수를 작성하는 프로그래머 입장에서는 `employee` 객체의 `salary`, `name`에 모두 접근할 수 있습니다. 하지만 다음과 같은 코드는 아예 컴파일 자체가 안 됩니다.

```cpp
#include <iostream>
#include <string>

struct employee {

// After this, all members are private
// until another specifier happens
private:

    int salary;
    std::string name;
};

int main(void) {
    employee blog_author(123, "wow");
    std::cout << blog_author.name << std::endl;
    std::cout << blog_author.salary << std::endl;
}
```

이런 식으로 어떤 클래스의 특정 멤버가 그 클래스 바깥에서 사용되지 못하도록 막는 키워드(`public`, `protected`, `private`)들을 두고 Access Specifier라고 합니다.
(위에서는 쉬운 이해를 위해 오직 변수만을 예시로 들었습니다.
하지만 변수, 함수, 타입, alias 등이 모두 access specifier에 의해 참조 범위가 제어될 수 있습니다.)

### This is for compilation only

코드 상으로 특정 변수가 어떤 클래스 바깥에서 사용되지 못한다고 해서, 코드를 컴파일했을 때 그 변수의 접근에 관하여 런타임 상에서 해당 변수 접근에 대한 일종의 보호막 역할을 하는 코드가 생성이 되는 것은 아닙니다.
다만 access specifier가 지정한 엑세스 범위를 위반하는 코드에 대해서 컴파일 타임에 컴파일러가 오류를 내뿜는 것일 뿐입니다.
만약 여러분이 직접 binary file의 instruction을 수정한다면, 코드에서 명시한 엑세스 범위를 무시하고 특정 변수를 아무 때나 엑세스할 수 있습니다.

### Range of coverage

3가지 access specifier에 대해 각 키워드는 다음과 같은 범위를 지정합니다.

- `public`: 그 어디에서든지 엑세스 가능한 멤버에 대한 access specifier입니다.
- `protected`: 해당 클래스 내부, 그리고 그 클래스를 상속받는 클래스에서 엑세스 가능한 멤버에 대한 access specifier입니다.
- `private`: 해당 클래스 내부에서만 엑세스 가능한 멤버에 대한 access specifier입니다.

예를 들면 다음과 같습니다.

```cpp
#include <iostream>

struct L1 {
public:
    void display() {
        std::cout << t << std::endl;
    }

protected:
    virtual void hello() {
        t++;
    }

private:
    int t;
};

struct L2: L1 {
public:
    void hello_L1() {
        L1::hello();
    }

protected:
    virtual void hello() override final {
        // t--; // Compile error!
    }

private:
    int t2;
};

int main(void) {
    L1 obj1; L2 obj2;
    obj1.display();
    // obj1.hello(); // Compile Error!
    // obj1.t; // Compile Error!
    obj2.hello_L1();
    // obj2.hello(); // Compile Error!
    // obj2.t2; // Compile Error!
}
```

---

## Access Specifiers in Inheritance

`class`/`struct`가 상속을 받을 때도 access specifier를 사용합니다.
예를 들어, *private inheritance*를 사용하면 경우에 따라 부모 클래스의 그 어떤 멤버도 자식 클래스에서 접근하지 못하게 하고 싶을 수도 있고 그게 가능합니다.
syntax는 다음과 같습니다;

```cpp
class Lbase {};
class Lpublic: public Lbase {};
class Lprotected: protected Lbase {};
class Lprivate: private Lbase {};
class Lmulti: public Lpublic, private Lprivate {};
```

### Access permission changes on inheritance

다음 표는 각 inheritance 종류에 대해 자식 클래스가 가지게 되는 부모 클래스 멤버에 대한 접근 권한을 나타낸 것입니다.

| | public member | protected member | private member | 
| -- | -- | -- | -- |
| public inheritance | remains same | remains same | inaccessible |
| protected inheritance | becomes protected | remains same | inaccessible |
| private inheritance | becomes private | becomes private | inaccessible |

### Difference between `class` and `struct`

C++에서 `class`와 `struct` 사이의 유일한 차이는 바로 default access specifier가 다르다는 점입니다.
`class`에서는 access specifier를 명시하지 않으면 기본적으로 컴파일러에서 `private`로 간주합니다.
하지만 `struct`에서는 access specifier를 명시하지 않으면 기본적으로 컴파일러에서 `public`으로 간주합니다.

클래스를 상속받는 과정에서의 default access specifier도 마찬가지입니다. `struct`는 기본적으로 public inheritance가 default 값이지만 `class`는 private inheritance가 default 값입니다.

---

## Access Specifiers Details

- Local class(nested class definition에 의해 정의된 클래스)는 그 클래스가 속한 바깥 클래스가 엑세스할 수 있는 모든 멤버를 엑세스할 수 있습니다.
    ```cpp
    class L1 {
    public:
        static int pub;
        class L2public {
        public:
            void access_test() {
                pub;
                pro;
                pri;
            }
        };

    protected:
        static int pro;

    private:
        static int pri;
    };
    ```
- `typedef`, `using` 등으로 정의된 alias들은 그 alias가 가리키는 것의 엑세스 범위가 아닌, 그 자체가 속한 access specifier의 엑세스 범위를 가집니다.
    ```cpp
    class L1 {
    private:
        class L2private {};
    public:
        typedef L2private L2public;
    };

    int main(void) {
        // L1::L2private x; // Compile Error!
        L1::L2public x;
    }
    ```
- Default function argument
- Access specifier에는 polymorphism이 적용되지 않습니다. 모든 expression의 access 범위는 해당 expression이 "컴파일 타임"에 가지는 변수 타입에 의해 결정됩니다. 이를 통해 access specifier를 우회할 수 있습니다.
    ```cpp
    #include <iostream>

    class L1 {
    public:
        virtual void f() {
            std::cout << "L1.f\n";
        }
    };

    class L2: public L1 {
    private:
        virtual void f() {
            std::cout << "L2.f\n";
        }
    };

    int main(void) {
        L2 o2;
        L1 *ptr1 = &o2;
        // o2.f(); // Compile Error!
        ptr1->f(); // prints "L2.f"
    }
    ```
- 클래스 내부에서 엑세스 불가능한 이름이라도 다른 방식으로 엑세스가 가능한 이름이면 access specifier를 우회할 수 있습니다.
    ```cpp
    class L1 {};
    class L2: private L1 {};
    class L3: public L2 {
        // L1 x; // Compile Error!
        ::L1 x; // OK
    };
    ```
- 다중상속에서, 어떤 멤버의 엑세스 범위는 그 멤버의 "최대 엑세스 범위"를 얻을 수 있는 inheritance path의 엑세스 범위와 동일합니다.
    ```cpp
    class L1 {
    public:
        int t;
    };

    class L2a: private virtual L1 {};
    class L2b: public virtual L1 {};

    class L3: public L2a, public L2b {
        void f() {
            t++; // Accessed through L2b (public)
        }
    };
    ```
- 어떤 class 객체가 메모리 상에 있을 때, 그 메모리 상에서 멤버들이 차지하는 메모리 fragment의 순서는 그 멤버들이 정의된 순서대로이지, access specifier에 의해서 좌우되지 않습니다.
- [StandardLayoutTypes](https://en.cppreference.com/w/cpp/named_req/StandardLayoutType) qualification을 통과하기 위해서는 모든 non-static member들이 같은 엑세스 범위를 가지고 있어야 합니다.
- 한 멤버가 서로 다른 두 class specifier 안에서 declare 될 수 없습니다.
    ```cpp
    class L1 {
    public:
        class L2;
    // private:
    //     class L2 {}; // Compile Error!
    };
    ```
- 부모 클래스의 protected 멤버를 엑세스할 때는 자기 클래스를 통해서 엑세스해야 합니다. Pointer to member variable을 만들 때도 마찬가지입니다.
    ```cpp
    class L1 {
    protected:
        int x;
    };

    class L2: public L1 {
        void f() {
            L1 *ptr; 
            // ptr->x; // Compile Error!
            this->x;
        }
    };
    ```
    ```cpp
    class L1 {
    protected:
        int x;
    };

    class L2: public L1 {
        void f() {
            // int L1::* ptr = &L1::x; // Compile Error!
            int L1::* ptr = &L2::x;
        }
    };
    ```
- 같은 타입의 서로 다른 두 객체의 같은 멤버의 엑세스 권한은 모두 같습니다.
    ```cpp
    class A {
    private:
        int member;
        void f(A x1, A x2) {
            x1.member;
            x2.member;
        }
    };
    ```