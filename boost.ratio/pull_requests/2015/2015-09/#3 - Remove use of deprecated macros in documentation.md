# #3 Remove use of deprecated macros in documentation [Merged]

> Username: mclow  
> Created at: 2015-09-11 22:23:12 UTC  
> Updated at: 2015-09-12 15:35:41 UTC  
> Merged at: 2015-09-12 06:50:50 UTC  
> Closed at: 2015-09-12 06:50:50 UTC  
> Url: https://github.com/boostorg/ratio/pull/3  

The documentation talks about the deprecated macro `BOOST_NO_STATIC_ASSERT` but the code (correctly) uses `BOOST_NO_CXX11_STATIC_ASSERT` instead.  
  
Update the docs to use the correct macro.

---
