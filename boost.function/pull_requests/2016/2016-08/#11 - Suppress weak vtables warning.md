# #11 Suppress weak vtables warning [Merged]

> Username: Kojoley  
> Created at: 2016-08-31 16:36:02 UTC  
> Updated at: 2018-11-02 22:16:31 UTC  
> Merged at: 2017-07-08 05:14:09 UTC  
> Closed at: 2017-07-08 05:14:09 UTC  
> Url: https://github.com/boostorg/function/pull/11  

Fixes following warning:  
  
```  
In file included from qi/actions2.cpp:20:  
In file included from ../../../boost/spirit/include/qi.hpp:16:  
In file included from ../../../boost/spirit/home/qi.hpp:21:  
In file included from ../../../boost/spirit/home/qi/nonterminal.hpp:14:  
In file included from ../../../boost/spirit/home/qi/nonterminal/rule.hpp:16:  
In file included from ../../../boost/function.hpp:24:  
In file included from ../../../boost/function/detail/prologue.hpp:17:  
../../../boost/function/function_base.hpp:696:7: warning: 'bad_function_call' has no out-of-line virtual method definitions; its vtable will be emitted in every translation unit [-Wweak-vtables]  
class bad_function_call : public std::runtime_error  
      ^  
```

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-09-04 22:16:30 UTC  
> Url: https://github.com/boostorg/function/pull/11#issuecomment-244631863  

In which version(s) of clang is this warning being produced ? I am wondering if moving the bad_function_call definition out of the class would mean that the warning would go away in clang ?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-09-04 22:35:05 UTC  
> Url: https://github.com/boostorg/function/pull/11#issuecomment-244632593  

> In which version(s) of clang is this warning being produced ?  
  
I've tried only with 3.8, but according to [this](https://github.com/llvm-mirror/clang/commits/4ab9d6e02b29c24ca44638cc61b52cde2df4a888/test/SemaCXX/warn-weak-vtables.cpp) and [clang release map](http://llvm.org/releases/) the warning was implemented since 2.7 release.  
  
> I am wondering if moving the bad_function_call definition out of the class would mean that the warning would go away in clang ?  
  
I will try, but I think it will not help, as the reason of the warning is the `std::runtime_error` vtable (because of virtual destructor and `what` function) in the class, which will not go away with an out of class definition.

---

## Comment 3

> Username: mclow  
> Created_at: 2016-09-04 22:39:34 UTC  
> Url: https://github.com/boostorg/function/pull/11#issuecomment-244632743  

The way we deal with this in libc++ is to put the destructor in a separately compiled module (i.e, not inline).  
  
But that will make Boost.Function not a header-only library.

---

## Comment 4

> Username: eldiener  
> Created_at: 2016-09-04 23:14:16 UTC  
> Url: https://github.com/boostorg/function/pull/11#issuecomment-244634152  

Making Boost.Function not a header-only library is definitely not the way to go. I hate warnings which are emitted at the usual warning levels which have nothing to do with real code problems, just possible optimization niggles. Why a vtable being emitted for each TU should be a real warning is beyond me.

---
