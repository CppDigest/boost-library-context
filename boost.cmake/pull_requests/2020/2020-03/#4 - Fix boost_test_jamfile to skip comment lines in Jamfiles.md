# #4 Fix boost_test_jamfile to skip comment lines in Jamfiles [Closed]

> Username: mloskot  
> Created at: 2020-03-27 21:18:52 UTC  
> Updated at: 2020-03-27 22:52:44 UTC  
> Closed at: 2020-03-27 22:52:37 UTC  
> Url: https://github.com/boostorg/cmake/pull/4  

Otherwise, the function generates targets for commented invocations  
of run, compile, etc. rules.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-03-27 22:41:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/4#issuecomment-605344648  

Does it?

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-03-27 22:52:37 UTC  
> Url: https://github.com/boostorg/cmake/pull/4#issuecomment-605347236  

No, it does not!  
  
I made a mistake when investigating why I'm perhaps missing some targets. Apologies for the noise.

---
