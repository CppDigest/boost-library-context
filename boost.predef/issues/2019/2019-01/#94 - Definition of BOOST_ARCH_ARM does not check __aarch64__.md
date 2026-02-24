# #94 - Definition of BOOST_ARCH_ARM does not check __aarch64__ [Closed]

> Username: s-shestakov  
> Created at: 2019-01-02 18:42:46 UTC  
> Updated at: 2019-01-18 15:12:05 UTC  
> Closed at: 2019-01-07 22:28:45 UTC  
> Url: https://github.com/boostorg/predef/issues/94  

In the header file https://github.com/boostorg/predef/blob/develop/include/boost/predef/architecture/arm.h macros __aarch64_ should be checked along with __arm64. Without that, we have some problems when building boost with Android NDK for arm64, because BOOST_ARCH_ARM is not defined.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-01-07 17:21:58 UTC  
> Url: https://github.com/boostorg/predef/issues/94#issuecomment-452011620  

Can you provide the output of all the predefs when targetting arm64 in your case? Should be easy to do with something like:  
```  
touch empty.cpp  
clang -E -dM empty.cpp | sort  
```  
I want to make sure I get the exact predef names correct to fix this.

---

## Comment 2

> Username: s-shestakov  
> Created at: 2019-01-18 08:51:39 UTC  
> Url: https://github.com/boostorg/predef/issues/94#issuecomment-455471372  

Command  
clang -target aarch64 -E -dM empty.cpp | sort  
  
[output.txt](https://github.com/boostorg/predef/files/2772193/output.txt)  
  
See also https://developer.android.com/ndk/guides/cpu-features

---

## Comment 3

> Username: grafikrobot  
> Created at: 2019-01-18 15:12:05 UTC  
> Url: https://github.com/boostorg/predef/issues/94#issuecomment-455578682  

> [output.txt](https://github.com/boostorg/predef/files/2772193/output.txt)  
  
Thanks for that.. I'll update the fix if needed with that info.
