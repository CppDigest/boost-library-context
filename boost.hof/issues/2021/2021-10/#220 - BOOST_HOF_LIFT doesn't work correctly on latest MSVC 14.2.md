# #220 - BOOST_HOF_LIFT doesn't work correctly on latest MSVC 14.2 [Open]

> Username: pdimov  
> Created at: 2021-10-17 03:46:51 UTC  
> Updated at: 2021-10-18 17:29:16 UTC  
> Url: https://github.com/boostorg/hof/issues/220  

https://godbolt.org/z/81bKWTx9M  
  
Using BOOST_HOF_LIFT at function scope fails with  
  
```  
<source>(14): error C2951: template declarations are only permitted at global, namespace, or class scope  
<source>(14): error C2892: a template cannot be a member of a local class  
<source>(15): fatal error C1506: unrecoverable block scoping error  
```  
  
whereas it works correctly under GCC and Clang. The reason is probably https://github.com/boostorg/hof/blob/0a28586156eb6cc7db1fbe74ae2a220daf40df14/include/boost/hof/lift.hpp#L89, which only checks whether `_MSC_VER` is defined, but not its value.  
  
In addition, checking `_MSC_VER` before `__clang__` is wrong because clang-cl defines both.

---

## Comment 1

> Username: pfultz2  
> Created at: 2021-10-18 16:40:08 UTC  
> Url: https://github.com/boostorg/hof/issues/220#issuecomment-945960367  

> which only checks whether _MSC_VER is defined, but not its value.  
  
What value should I check for?  
  
> In addition, checking _MSC_VER before __clang__ is wrong because clang-cl defines both.  
  
I'll switch the order.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-10-18 16:58:52 UTC  
> Url: https://github.com/boostorg/hof/issues/220#issuecomment-945974562  

I don't know what versions of MSVC work correctly and what do not; which one did you test with when you added the workaround?

---

## Comment 3

> Username: pfultz2  
> Created at: 2021-10-18 17:05:22 UTC  
> Url: https://github.com/boostorg/hof/issues/220#issuecomment-945980191  

> which one did you test with when you added the workaround?  
  
I originally tested this with 14 and 15. It looks like the workaround stops working in 19.20 in godbolt, so I will at least disabled it in that version.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-10-18 17:08:14 UTC  
> Url: https://github.com/boostorg/hof/issues/220#issuecomment-945982661  

Turns out, it's pretty involved. The "normal" (not Clang) implementation works under VS2017 in both [C++14](https://godbolt.org/z/YzWjnfKzz) and [C++17](https://godbolt.org/z/ocMacEvMx) modes. It also works with VS2019 in [C++14](https://godbolt.org/z/o4fYP353o) and [C++20](https://godbolt.org/z/6hqYWen4Y) mode. It however fails in [C++17](https://godbolt.org/z/r1n34oErP) mode in all VS2019 updates [except the very first one](https://godbolt.org/z/69c7WTvfT).  
  
I haven't checked how the Clang-specific version fares under all these.  
  
Your code is quite taxing. :-)

---

## Comment 5

> Username: pfultz2  
> Created at: 2021-10-18 17:29:16 UTC  
> Url: https://github.com/boostorg/hof/issues/220#issuecomment-945999530  

> The "normal" (not Clang) implementation works under VS2017  
  
Yea they updated VS2017 in appveyor and there have been test failures. I should probably drop this version.   
  
> It however fails in C++17 mode in all VS2019 updates except the very first one.  
  
Hmm, interesting.  It seems the clang workaround works in this scenario. Maybe I should switch to use that for all MSVC versions.   
  
> Your code is quite taxing. :-)  
  
Yea, returning a generic lambda doesn't work in all cases on MSVC, but I found I could create a local templated class and return it from a lambda function.   
  
The clang workaround was needed because noexcept was failing on some test cases(so I pass `noexcept` separately).
