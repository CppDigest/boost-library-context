# #37 Remove use of deprecated macros [Merged]

> Username: mclow  
> Created at: 2015-09-11 22:18:05 UTC  
> Updated at: 2015-09-13 21:58:29 UTC  
> Merged at: 2015-09-13 20:52:09 UTC  
> Closed at: 2015-09-13 20:52:09 UTC  
> Url: https://github.com/boostorg/range/pull/37  

In as_literal.hpp, there were several uses of the deprecated macros `BOOST_NO_CHAR16_T` and `BOOST_NO_CHAR32_T`. Change them to use the replacement macros `BOOST_NO_CXX11_CHAR16_T` and `BOOST_NO_CXX11_CHAR32_T`.  
  
No functionality change.

---
