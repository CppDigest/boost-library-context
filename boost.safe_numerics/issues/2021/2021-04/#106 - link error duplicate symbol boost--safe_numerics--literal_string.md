# #106 - link error duplicate symbol boost::safe_numerics::literal_string [Closed]

> Username: Klayflash  
> Created at: 2021-04-01 16:42:55 UTC  
> Updated at: 2021-05-18 22:40:43 UTC  
> Closed at: 2021-05-18 22:40:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/106  

Hello.  
  
Thanks for useful library!  
  
Today I started updating from boost_1_74_0 to boost_1_75_0 and linking has failed. With message:  
  
```  
lld-link: error: duplicate symbol: char const * __cdecl boost::safe_numerics::literal_string(enum boost::safe_numerics::safe_numerics_error const &)  
>>> defined at D:\lib\boost_1_75_0\boost\safe_numerics\exception.hpp:48  
```  
literal_string function is introduced in boost_1_75_0.  
I think it is same case as for make_error_condition function in exception.hpp. The function was marked by "#if 0" with "lead duplicate symbol" note.

---

## Comment 1

> Username: Klayflash  
> Created at: 2021-04-01 17:03:26 UTC  
> Updated at: 2021-04-01 17:05:28 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/106#issuecomment-812044718  

I see it was already marked in "Version 1.76.0 beta 1" as  
`constexpr inline const char`  
I confirm fix.  
  
Proposed solution to prevent duplicate symbols is adding dummy.cpp that contains all library includes. And link dummy.cpp to tests.

---

## Comment 2

> Username: Klayflash  
> Created at: 2021-04-01 17:04:37 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/106#issuecomment-812045396  

related: https://github.com/boostorg/safe_numerics/issues/94 https://github.com/boostorg/safe_numerics/issues/66
