# #75 Guard attribute names with double underscores [Merged]

> Username: morinmorin  
> Created at: 2015-09-03 10:06:28 UTC  
> Updated at: 2015-09-10 17:23:24 UTC  
> Merged at: 2015-09-07 16:32:06 UTC  
> Closed at: 2015-09-07 16:32:06 UTC  
> Url: https://github.com/boostorg/config/pull/75  

Both gcc and clang (since their early versions like gcc-2.9.5 and clang-1.0) accept attribute names with double underscores to avoid macro interference. So it's better to guard attribute names with double underscores, but there are unguarded ones in `clang.hpp` and `gcc.hpp`:   
  
```  
#  define BOOST_ATTRIBUTE_UNUSED __attribute__((unused))  
```  
  
This PR fix this.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-09-07 16:32:04 UTC  
> Url: https://github.com/boostorg/config/pull/75#issuecomment-138336041  

Thanks for that!

---
