# #32 - Math functions can't be specialized to custom templated scalar types [Closed]

> Username: federkamm  
> Created at: 2021-07-18 22:08:39 UTC  
> Updated at: 2021-07-22 06:05:53 UTC  
> Closed at: 2021-07-19 07:19:19 UTC  
> Url: https://github.com/boostorg/qvm/issues/32  

I'm trying to use QVM with Google Ceres which has a templated scalar type `Jet<typename T, int N>` for it's automatic differentiation framework.  It works well for operations like vector addition and matrix multiplication using some custom traits like  
  
    template <typename T, int N>  
    struct deduce_scalar<S, Jet<T, N>> { using type = Jet<typename deduce_scalar<S, T>::type, N>; };  
  
but fails for operations like `mag` which call math functions like `sqrt` in this case.  In order to make it work one would have to partially specialize the `boost::qvm::sqrt` function to  
  
    template <typename T, int N>  
    Jet<T, N> sqrt<Jet<T, N>>(Jet<T, N> x) { return ceres::sqrt(x); }  
  
but unfortunatelly, C++ doesn't allow for partial function specializations.  
  
To fix this, I think the idiomatic way for C++ would be to use function overloading with argument dependend lookup (ADL) instead of function templating.  
  
I.e. in the `mag` function `T const mag=sqrt<T>(m2);` should be replaced by  
  
    using std::sqrt;  
    T const mag=sqrt(m2);  
  
As I understand ADL, this would look for any function of type `sqrt(T)` in the namespace `boost::qvm`, the global namespace `::`, in the namespace that defined the type `T`, and would fallback to `std::sqrt` otherwise to handle the cases for `double`, `float` and the integer types (alternatively, you could define two overloaded functions `boost::qvm::sqrt(double)` and `boost::qvm::sqrt(float)` without templates and wouldn't need the `using std::sqrt` line).  
  
For my case, this would work out of the box, as the type `Jet` of my concern is defined in namespace `ceres` which also contains a `sqrt(Jet<T, N>)` function.  For other cases, one could provide an overload of `sqrt` in the namespace of the custom type, the global namespace, or within `boost::qvm` similar to how can be done for non-template types already now, just without templates.  
  
Alternatively, you could make a class  
  
    template <typename T>  
    struct Sqrt {  
        static T calc(T x) { return sqrt<T>(x); }  
    };  
  
that falls back to the `sqrt` template in `boost::qvm` (in case anyone has already specialized it for their own types in client code) but can be partially specialized for custom templated types.  Or you could make a class that specializes all math functions for one type at once:  
  
    template <typename T>  
    struct Math {  
        static T sqrt(T x) { return boost::qvm::sqrt<T>(x); }  
        static T sin(T x) { return boost::qvm::sin<T>(x); }  
        // ...  
    };  
  
I could probably provide a pull request but I would like you to help to decide which way to go to allow for custom templated scalar types (if you want to support them).  I think I would vote for the ADL solution, but only because I imagine that this would be what an experienced C++ developer would go for.  However, I wouldn't count me to that category of people, at least not regarding this type of library code.

---

## Comment 1

> Username: zajo  
> Created at: 2021-07-19 01:29:07 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-882159670  

All good points.  
  
The reason I was invoking the qvm function templates explicitly was to make  
damn sure that you don't end up calling the standard overload for double  
when you pass a float, which would be a nasty performance bug. An  
unqualified call works fine, but I don't think it's a good idea to do  
  
using std::sqrt;  
T const mag=sqrt(m2);  
  
This would probably introduce ambiguities. For example, there is a function  
template qvm::sqrt<float> that takes a float but there's also the standard  
function sqrt that takes a double.  
  
I made the change you're requesting, see  
https://github.com/boostorg/qvm/commit/46797f11f71c625420217ef9043d6ee4a12cf8be.  
Note that I have only tested on Mac OS, at this time we're waiting on GHA  
for the rest: https://github.com/boostorg/qvm/actions/runs/1043592047.

---

## Comment 2

> Username: zajo  
> Created at: 2021-07-19 03:00:45 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-882194485  

On Sun, Jul 18, 2021 at 6:28 PM Emil Dotchevski ***@***.***>  
wrote:  
> I made the change you're requesting, see  
https://github.com/boostorg/qvm/commit/46797f11f71c625420217ef9043d6ee4a12cf8be.  
Note that I have only tested on Mac OS, at this time we're waiting on GHA  
for the rest: https://github.com/boostorg/qvm/actions/runs/1043592047.  
  
>  
This is now in develop; federkamm, please confirm it works for you.

---

## Comment 3

> Username: federkamm  
> Created at: 2021-07-19 07:19:19 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-882306090  

Wow, that was fast.  I can confirm that it does work for me, but I have an unrelated issue with the current master branch for which I'll open a new issue.  
  
Currently, I think the resolution order favors `boost::qvm::sqrt<float>(float)` over `::sqrt(double)` for `float` types but I think it would favor a `::sqrt(float)` over the templated version, if someone included such in their project.  I think a simple overload in `boost::qvm::sqrt(float)` instead of a template would probably prevent this and might even be favored over a global `::sqrt(float)` function.  However, I'm never sure about this kind of things without testing.

---

## Comment 4

> Username: zajo  
> Created at: 2021-07-19 10:01:03 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-882417045  

Can you produce an example that demonstrates the problem?

---

## Comment 5

> Username: federkamm  
> Created at: 2021-07-19 11:31:56 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-882475345  

I added the example to issue #33

---

## Comment 6

> Username: zajo  
> Created at: 2021-07-19 17:55:53 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-882743983  

No, I mean an example about resolution order above. The other issue is pretty clear, I'll take a look.

---

## Comment 7

> Username: federkamm  
> Created at: 2021-07-20 15:19:49 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-883479518  

I would make a test environment like this:  
  
```c++  
#include <iostream>  
#include <type_traits>  
namespace my {  
struct W { operator double () { return 1.0; } };  
int f(W) { return 0; }  
int f(double) { return 1; }  
int f(float) { return 2; }  
int f(int) { return 3; }  
template <typename T> int f(T);  
//template <typename T> std::enable_if_t<std::is_floating_point<T>::value, int> f(T);  
template <> int f<double>(double) { return 4; }  
template <> int f<float>(float) { return 5; }  
template <> int f<int>(int) { return 6; }  
}  
int f(double) { return 7; }  
int f(float) { return 8; }  
int f(int) { return 9; }  
template <typename T> int f(T);  
//template <typename T> std::enable_if_t<std::is_floating_point<T>::value, int> f(T);  
template <> int f<double>(double) { return 10; }  
template <> int f<float>(float) { return 11; }  
template <> int f<int>(int) { return 12; }  
namespace parent {  
int f(double) { return 13; }  
int f(float) { return 14; }  
int f(int) { return 15; }  
//template <typename T> int f(T);  
template <typename T> std::enable_if_t<std::is_floating_point<T>::value, int> f(T);  
template <> int f<double>(double) { return 16; }  
template <> int f<float>(float) { return 17; }  
//template <> int f<int>(int) { return 18; }  
namespace inner {  
//int f(double) { return 19; }  
//int f(float) { return 20; }  
//int f(int) { return 21; }  
//template <typename T> int f(T);  
//template <typename T> std::enable_if_t<std::is_floating_point<T>::value, int> f(T);  
//template <> int f<double>(double) { return 22; }  
//template <> int f<float>(float) { return 23; }  
//template <> int f<int>(int) { return 24; }  
int f() { return 25; }  
void fun() {   
  std::cout << " " << f(1.0);  
  std::cout << " " << f(1.0f);  
  std::cout << " " << f(1);  
  std::cout << " " << f(my::W{});  
  std::cout << "\n";  
}  
}}  
int main() {  
  parent::inner::fun();  
  {  
    using namespace parent::inner;  
    fun();  
  }  
  {  
    using namespace parent;  
    inner::fun();  
  }  
  {  
    using namespace my;  
    parent::inner::fun();  
  }  
  {  
    using namespace my;  
    using namespace parent;  
    inner::fun();  
  }  
  return 0;  
}  
```  
  
and analyze the situation by commenting and uncommenting different lines.  My impression so far is, that it doesn't matter what namespaces are visible to the caller of `fun` (i.e. the four calls from main are identical), that ADL always wins (if not uncommented, the last call is always `0`) and that otherwise, lookup stops in `parent::inner` if any symbol `f` is found there and than the "best" one is chosen.  The overload `parent::inner::f(T)` would win over the template `parent::inner::f<T>(T)` but that doesn't matter since any `f` in `parent::inner` wins over any `f` outside.  Therefore, your current solution seems to be more or less "safe", i.e. it always calls the `boost::qvm::sqrt<T>(T)` version for `float` and `double`.  However, I think it would fail for integral types with linker error "couldn't find `boost::qvm::sqrt<int>(int)`" or similar.  You might want to consider to guard your templated `boost::qvm::sqrt<T>` with an `enable_if<is_floating_point>` trait (or switch from templates to overloads).  
  
For custom types `C`, I think the current solution is working.  For e.g. `sqrt` in the same namespace as `C`, that function is called by ADL.  If it doesn't exist, one can define it, can specialize `boost::qvm::sqrt<C>(C)` if `C` is not a template by itself, or use a overload `template <typename T> boost::qvm::sqrt(C<T>)` for templated types.

---

## Comment 8

> Username: zajo  
> Created at: 2021-07-20 19:14:57 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-883631628  

> lookup stops in parent::inner if any symbol f is found there  
  
Yes, the technical term for this is name hiding. It seems you're saying there's no problem with the current `develop` implementation (I didn't think there was).  
  
I'm aware of the link error for integer types. The reason QVM provides math function overloads is to be able to do automatic overload resolution between e.g. `std::sin`/`std::sinf`. If QVM defined the main template `qvm::sin` (rather than just specializations for `float` and `double`), should it call `std::sin` or `std::sinf`? The link error forces the user to choose, which is a good thing.

---

## Comment 9

> Username: federkamm  
> Created at: 2021-07-20 19:42:36 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-883647364  

Yes, I think the current develop implementation does the right thing. But I thought, `sinf` and `sinl` in `std` exist only for  better C-code compatibility and that `std::sin` is supposed to choose the correct overload for `float`, `double` and `long  double` by its own (at least it defines all three overloads). Doesn't it do it correctly?

---

## Comment 10

> Username: zajo  
> Created at: 2021-07-21 06:23:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-883925927  

The math functions are ANSI C. Per C++ convention, if a C++ program uses `#include <math.h>`, we get e.g. `sin` (the `double` version) and `sinf` (the `float` version) declared in the global namespace. If instead we `#include <cmath>`, we get the same functions but defined in namespace `std`, as to avoid polluting the global namespace, in theory. In practice the global namespace is already polluted. :)  
  
The function templates defined by `boost/qvm/math.hpp` in `namespace qvm` serve the purpose you had in mind.

---

## Comment 11

> Username: federkamm  
> Created at: 2021-07-21 06:51:27 UTC  
> Updated at: 2021-07-21 06:54:32 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-883938577  

`sin(double)`, `sinf(float)`, and `sinl(long double)` are provided in global namespace in `math.h` for C-compatibility.  Since C++11, `sinf(double)` and `sinl(long double)` are also provided within `std` namespace in `math.h` and `cmath`.  Before, they only provided an overloaded `std::sin` function for `double`, `float`, `long double`, and `IntegralType`s (don't know, who would ever need the `sin` of an integral number of radians only) which is supposed to choose the "best" algorithm for the datatype by overload resolution.  So indeed, there is a difference between the functions in the global namespace (no overloads) and the functions provided in the `std` namespace (with overloads).  The standard says to that:  
  
## 20.2  The C standard library [library.c]  
  
1. The C++standard library also makes available the facilities of the C standard library, suitably adjusted to ensure static type safety.  
  
2. The descriptions of many library functions rely on the C standard library for the semantics of those functions. In some cases, the signatures specified in this document may be different from the signatures in the C standard library, and additional overloads may be declared in this document, but the behavior and the preconditions (including any preconditions implied by the use of an ISO C `restrict` qualifier) are the same unless otherwise stated.  
  
### 29.9.1 Header `<cmath>` synopsis [cmath.syn]  
  
```c++  
// [...]  
namespace std {  
  // [...]  
  float sin(float x); // see 20.2  
  double sin(double x);  
  long double sin(long double x); // see 20.2  
  float sinf(float x);  
  long double sinl(long double x);  
  // [...]  
}  
```

---

## Comment 12

> Username: zajo  
> Created at: 2021-07-22 06:05:53 UTC  
> Url: https://github.com/boostorg/qvm/issues/32#issuecomment-884675980  

News to me, thanks.
