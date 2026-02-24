# #857 - Build failed with Clang 16.0.0 [Closed]

> Username: DevTester888  
> Created at: 2024-02-02 03:12:48 UTC  
> Updated at: 2024-03-20 13:49:08 UTC  
> Closed at: 2024-03-20 13:49:08 UTC  
> Url: https://github.com/boostorg/boost/issues/857  

I get an build error. The log message is "integer value -1 is outside the valid range of values [0, 3] for this enumeration type" in integral_wrapper.hpp. This is a new clang feature, https://releases.llvm.org/16.0.0/tools/clang/docs/ReleaseNotes.html#id87 .  
Has any idea for this issue? Please help me, thanks.  
  
<img width="1385" alt="boost_build_error" src="https://github.com/boostorg/boost/assets/142870018/ee7c1cd4-53ce-43e5-824f-789bbc6c5228">

---

## Comment 1

> Username: pavel-trafimuk  
> Created at: 2024-02-29 09:31:50 UTC  
> Url: https://github.com/boostorg/boost/issues/857#issuecomment-1970744815  

Have the same issue

---

## Comment 2

> Username: haronaut  
> Created at: 2024-03-20 07:28:08 UTC  
> Url: https://github.com/boostorg/boost/issues/857#issuecomment-2008943802  

same here clang 18

---

## Comment 3

> Username: mclow  
> Created at: 2024-03-20 13:49:08 UTC  
> Url: https://github.com/boostorg/boost/issues/857#issuecomment-2009612756  

This is a duplicate of https://github.com/boostorg/mpl/issues/69
