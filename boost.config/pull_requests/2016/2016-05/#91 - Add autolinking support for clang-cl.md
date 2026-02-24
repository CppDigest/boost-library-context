# #91 Add autolinking support for clang-cl [Closed]

> Username: ismail  
> Created at: 2016-05-29 14:39:42 UTC  
> Updated at: 2016-05-29 14:42:35 UTC  
> Closed at: 2016-05-29 14:42:35 UTC  
> Url: https://github.com/boostorg/config/pull/91  

clang-cl emulates all of the cl.exe options so we can fake it as BOOST_MSVC as far as autolinking is concerned and undef it at the end. Tested with boost 1.61.0 with clang 3.9 trunk.

---

## Comment 1

> Username: ismail  
> Created_at: 2016-05-29 14:42:35 UTC  
> Url: https://github.com/boostorg/config/pull/91#issuecomment-222364225  

Doesn't seem to work with boost::thread, closing for now :/

---
