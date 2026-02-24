# #108 Suppress `-Wdeprecated-copy` on Clang 13 [Merged]

> Username: Kojoley  
> Created at: 2022-09-08 23:00:34 UTC  
> Updated at: 2022-10-05 21:34:09 UTC  
> Merged at: 2022-09-12 22:35:59 UTC  
> Closed at: 2022-09-12 22:35:59 UTC  
> Url: https://github.com/boostorg/phoenix/pull/108  

Clang 13 now also emits `-Wdeprecated-copy` when a copy assign operator is deleted. Since we need both `actor` to be an aggregate and to disallow assignment -- the only other way is to suppress the warning.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2022-09-12 19:50:32 UTC  
> Url: https://github.com/boostorg/phoenix/pull/108#issuecomment-1244296761  

@djowel I've fixed the CI if that was blocking the merge.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-10-05 11:01:06 UTC  
> Url: https://github.com/boostorg/phoenix/pull/108#issuecomment-1268279307  

Why not handle the warning? I failed to reproduce the warning with a simple example only deleting the assignment operator and found that `is_pod` still yields true when the default constructor is defaulted: https://godbolt.org/z/oaE8ndE3x

---

## Comment 3

> Username: Kojoley  
> Created_at: 2022-10-05 21:34:05 UTC  
> Url: https://github.com/boostorg/phoenix/pull/108#issuecomment-1269003390  

From [\[dcl.init.aggr\] p1](https://eel.is/c++draft/dcl.init.aggr#1):  
> An [aggregate](https://eel.is/c++draft/dcl.init.aggr#def:aggregate) is an array or a class ([[class]](https://eel.is/c++draft/class)) with  
>  
> * no user-declared or inherited constructors ([[class.ctor]](https://eel.is/c++draft/class.ctor)),  
  
From [\[diff.cpp17\] p3](https://eel.is/c++draft/diff.cpp17#dcl.dcl-3):  
  
> **Affected subclause**: [\[dcl.init.aggr\]](https://eel.is/c++draft/dcl.init.aggr)  
> **Change**: A class that has user-declared constructors is never an aggregate.  
> **Rationale**: Remove potentially error-prone aggregate initialization which may apply notwithstanding the declared constructors of a class.  
> **Effect on original feature**: Valid C++ 2017 code that aggregate-initializes a type with a user-declared constructor may be ill-formed or have different semantics in this revision of C++. For example:  
> ```cpp  
> struct A {              // not an aggregate; previously an aggregate  
>   A() = delete;  
> };  
>   
> struct B {              // not an aggregate; previously an aggregate  
>   B() = default;  
>   int i = 0;  
> };  
>   
> struct C {              // not an aggregate; previously an aggregate  
>   C(C&&) = default;  
>   int a, b;  
> };  
>   
> A a{};                  // ill-formed; previously well-formed  
> B b = {1};              // ill-formed; previously well-formed  
> auto* c = new C{2, 3};  // ill-formed; previously well-formed  
>   
> struct Y;  
>   
> struct X {  
>   operator Y();  
> };  
>   
> struct Y {              // not an aggregate; previously an aggregate  
>   Y(const Y&) = default;  
>   X x;  
> };  
>   
> Y y{X{}};               // copy constructor call; previously aggregate-initialization  
> ```  
  
https://godbolt.org/z/946Gs14GP

---
