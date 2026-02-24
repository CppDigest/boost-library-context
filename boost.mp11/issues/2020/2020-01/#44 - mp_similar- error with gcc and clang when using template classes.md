# #44 - mp_similar: error with gcc and clang when using template classes [Closed]

> Username: tobias-loew  
> Created at: 2020-01-05 10:28:07 UTC  
> Updated at: 2020-01-18 22:42:06 UTC  
> Closed at: 2020-01-18 22:42:06 UTC  
> Url: https://github.com/boostorg/mp11/issues/44  

Hi,  
the current trunk versions of gcc and clang produce ambiguity errors when instantiating mp_similar with the same template class twice. e.g.  
  
#include <list>  
#include <boost/mp11.hpp>  
  
int main(){  
    static constexpr bool b = boost::mp11::mp_similar<std::list<int>, std::list<int>>::value;  
}  
  
  
I've already filed bug-reports:  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93155  
https://bugs.llvm.org/show_bug.cgi?id=44459  
  
Please find here an example:  
https://godbolt.org/z/Wzaa6_

---

## Comment 1

> Username: tobias-loew  
> Created at: 2020-01-05 11:49:21 UTC  
> Url: https://github.com/boostorg/mp11/issues/44#issuecomment-570904282  

After reading [temp.deduct.partial] I think that gcc and clang are right with flagging mp_similar_impl as ambiguous, since (MSVC erroneously can compile the example, but when compiling the equivalent function-template it also results in an ambiguity error  
```  
    template<class... T> struct mp_similar_impl {};  
  
    template<class T>   
    void f(mp_similar_impl<T, T>) {}  
  
    template<template<class...> class L, class... T1, class... T2>   
    void f(mp_similar_impl<L<T1...>, L<T2...>>) {}  
  
    int main() {  
        f(mp_similar_impl<std::list<int>, std::list<int>>{});  // error ambiguous  
   }  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2020-01-05 12:21:24 UTC  
> Url: https://github.com/boostorg/mp11/issues/44#issuecomment-570906868  

This should be fixed by https://github.com/boostorg/mp11/commit/f14309e92ddc5fd7f3263b25875cd62be2bb2711. Unfortunately I noticed it too late. :-)
