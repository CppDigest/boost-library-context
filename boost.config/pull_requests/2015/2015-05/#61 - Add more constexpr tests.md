# #61 Add more constexpr tests [Merged]

> Username: MarcelRaad  
> Created at: 2015-05-05 09:48:18 UTC  
> Updated at: 2015-05-05 11:12:13 UTC  
> Merged at: 2015-05-05 11:12:00 UTC  
> Closed at: 2015-05-05 11:12:00 UTC  
> Url: https://github.com/boostorg/config/pull/61  

MSVC 14 RC currently passes the constexpr tests, but fails to compile Boost if BOOST_NO_CXX11_CONSTEXPR is not defined. Added three tests that clang can compile in C++11 mode, but MSVC cannot.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-05-05 11:12:13 UTC  
> Url: https://github.com/boostorg/config/pull/61#issuecomment-99034815  

Thanks!

---
