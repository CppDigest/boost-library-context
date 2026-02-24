# #22 Fix {make,jump}_fcontext visibility with mingw [Merged]

> Username: xantares  
> Created at: 2015-09-28 08:22:18 UTC  
> Updated at: 2016-01-22 09:41:24 UTC  
> Merged at: 2015-09-28 15:26:46 UTC  
> Closed at: 2015-09-28 15:26:46 UTC  
> Url: https://github.com/boostorg/context/pull/22  

fixes #16, the first part of the fedora patch to force platform detection is not needed as it can be overriden from b2 arguments

---

## Comment 1

> Username: olk  
> Created_at: 2015-09-28 15:36:23 UTC  
> Url: https://github.com/boostorg/context/pull/22#issuecomment-143781113  

Did you execute the unit-tests?  
On my system the unit-tests fail - fix reverted!

---

## Comment 2

> Username: xantares  
> Created_at: 2015-09-28 15:42:59 UTC  
> Url: https://github.com/boostorg/context/pull/22#issuecomment-143783795  

No, how should I run them ? Did you use mingw on linux ?

---

## Comment 3

> Username: olk  
> Created_at: 2015-09-28 16:42:16 UTC  
> Url: https://github.com/boostorg/context/pull/22#issuecomment-143800014  

Please do not post untested patches.  
  
each boost library has a test sub directory - executing b2/bjam runs the unit-tests

---
