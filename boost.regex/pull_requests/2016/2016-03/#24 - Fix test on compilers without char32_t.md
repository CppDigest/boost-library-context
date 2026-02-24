# #24 Fix test on compilers without char32_t [Merged]

> Username: MarcelRaad  
> Created at: 2016-03-11 16:26:26 UTC  
> Updated at: 2016-03-12 17:51:59 UTC  
> Merged at: 2016-03-12 16:54:52 UTC  
> Closed at: 2016-03-12 16:54:52 UTC  
> Url: https://github.com/boostorg/regex/pull/24  

As the include for BOOST_NO_CXX11_CHAR32_T was missing, the test didn't compile.

---
