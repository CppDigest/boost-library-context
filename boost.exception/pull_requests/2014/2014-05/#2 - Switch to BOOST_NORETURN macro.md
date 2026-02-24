# #2 Switch to BOOST_NORETURN macro [Closed]

> Username: Lastique  
> Created at: 2014-05-31 18:38:42 UTC  
> Updated at: 2014-06-24 04:59:21 UTC  
> Closed at: 2014-06-04 20:47:30 UTC  
> Url: https://github.com/boostorg/exception/pull/2  

Boost.Config now defines BOOST_NORETURN macro which is equivalent to BOOST_ATTRIBUTE_NORETURN. boost/exception/detail/attribute_noreturn.hpp can be removed as well, if no longer needed anywhere.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-06-04 20:47:30 UTC  
> Url: https://github.com/boostorg/exception/pull/2#issuecomment-45149237  

Due to extraction to a new submodule, moved to https://github.com/boostorg/throw_exception/pull/1.

---
