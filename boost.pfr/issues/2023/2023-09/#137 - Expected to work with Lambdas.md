# #137 - Expected to work with Lambdas? [Open]

> Username: jonesmz  
> Created at: 2023-09-06 16:35:21 UTC  
> Updated at: 2024-10-17 09:11:12 UTC  
> Url: https://github.com/boostorg/pfr/issues/137  

Hi, I see in the Limitations section  
  
>  Boost.PFR library works with types that satisfy the requirements of SimpleAggregate: aggregate types without base classes, const fields, references, or C arrays:   
  
A casual reader may have seen on the internet "Lambdas are just syntax sugar around a struct with `operator()`", and expect that Boost.PFR would be able to introspect the types of the capture list of a lambda, since they are "just structs".  
  
Sadly, this does not appear to work in Boost.PFR.  
  
```  
#include <boost/pfr/tuple_size.hpp>  
  
void function()  
{  
    auto lambda = [](void)mutable{};  
    using lambda_t = decltype(lambda);  
    static_assert(0 == boost::pfr::tuple_size_v<lambda_t>);  
}  
```  
  
This gives the error:  
  
```  
D:\builds\cm-systest\system\pub\dists\boost\boost/pfr/detail/fields_count.hpp(285): error C2338: static_assert failed: '====================> Boost.PFR: Type must be aggregate initializable.'  
D:\builds\cm-systest\system\pub\dists\boost\boost/pfr/tuple_size.hpp(33): note: see reference to function template instantiation 'size_t boost::pfr::detail::fields_count<T>(void) noexcept' being compiled  
        with  
        [  
            T=lambda_t  
        ]  
```  
  
on the visual studio 2022 compiler, with similar errors on clang 16. (Both in C++17 mode, if that makes a difference).  
  
I think it might be helpful for people searching for libraries to do introspection if you clearly document that lambdas are or are not supported in the Limitations section, with a brief explanation of why not (e.g. not aggregate types, because compiler-magic).

---

## Comment 1

> Username: schaumb  
> Created at: 2023-11-22 18:14:37 UTC  
> Updated at: 2023-11-22 18:14:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/137#issuecomment-1823255300  

Lambdas are not aggregate types.  
See: https://eel.is/c++draft/expr.prim.lambda.closure#3

---

## Comment 2

> Username: denzor200  
> Created at: 2023-12-10 18:48:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/137#issuecomment-1849048118  

Definitely it will not work with lambdas :)  
Nobody discovered such technique

---

## Comment 3

> Username: schaumb  
> Created at: 2023-12-10 19:43:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/137#issuecomment-1849062614  

(These hacks are listed because if someone wants to experiment with lambdas):  
  
---  
  
It can check that it is layout compatible with a type list:  
```cpp  
    int a;  
    bool c;  
    auto xx = [a, c] {};  
    using type = decltype(xx);  
  
    struct CC {  
        int x;  
        bool y;  
    };  
  
    static_assert(std::is_layout_compatible_v<type, CC>);  
```  
  
---   
Compiler and lambda-specific member getters:  
GCC uses public variables with double underscore.  
```cpp  
int a;  
auto l = [a] {};  
int& lambda_a = l.__a;  
```  
MSVC uses the same named private variables :  
```cpp  
int a;  
auto l = [a] {};  
// somehow get the &decltype(l)::a , ex: https://github.com/martong/access_private  
```
