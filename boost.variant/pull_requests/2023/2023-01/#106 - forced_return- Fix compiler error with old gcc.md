# #106 forced_return: Fix compiler error with old gcc [Closed]

> Username: orgads  
> Created at: 2023-01-26 16:05:50 UTC  
> Updated at: 2024-01-26 08:12:26 UTC  
> Closed at: 2023-10-21 09:05:01 UTC  
> Url: https://github.com/boostorg/variant/pull/106  

GCC < 4.5 has BOOST_UNREACHABLE_RETURN defined as return x for some reason, and this is obviously wrong for this function. So revert 3cc73fe16 only for these platforms.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2023-10-21 09:05:01 UTC  
> Updated_at: 2023-10-21 09:05:22 UTC  
> Url: https://github.com/boostorg/variant/pull/106#issuecomment-1773725767  

Thanks for the report!  
  
However, C++03 is not supported any more and GCC < 4.5 have no C++11 support. Use older versions of Boost if support for antique compilers is reuired

---

## Comment 2

> Username: orgads  
> Created_at: 2023-10-21 17:08:35 UTC  
> Updated_at: 2023-10-21 17:15:32 UTC  
> Url: https://github.com/boostorg/variant/pull/106#issuecomment-1773865123  

@apolukhin Not supported by whom? Where is this documented?  
  
According to https://www.boost.org/doc/libs/1_83_0/ variant requires C++03:  
![image](https://github.com/boostorg/variant/assets/1246544/c45e2b4e-ff76-485a-91a7-6be08cf795f4)  
  
I refer to the condition here:  
https://github.com/boostorg/config/blob/master/include/boost/config/compiler/gcc.hpp#L345

---

## Comment 3

> Username: orgads  
> Created_at: 2024-01-25 20:51:44 UTC  
> Url: https://github.com/boostorg/variant/pull/106#issuecomment-1910977289  

@apolukhin ping?

---

## Comment 4

> Username: apolukhin  
> Created_at: 2024-01-26 08:07:45 UTC  
> Url: https://github.com/boostorg/variant/pull/106#issuecomment-1911639436  

Not supported since 1.84   
  
The warning about dropping C++03 support https://www.boost.org/users/history/version_1_82_0.html  
The notice in 1.84 changelog https://www.boost.org/users/history/version_1_84_0.html

---

## Comment 5

> Username: orgads  
> Created_at: 2024-01-26 08:12:24 UTC  
> Url: https://github.com/boostorg/variant/pull/106#issuecomment-1911644150  

Yes, I've just noticed it too. Ok then.  
  
Thanks.

---
