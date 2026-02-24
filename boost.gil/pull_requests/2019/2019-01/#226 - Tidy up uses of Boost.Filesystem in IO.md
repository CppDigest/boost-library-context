# #226 Tidy up uses of Boost.Filesystem in IO [Merged]

> Username: mloskot  
> Created at: 2019-01-27 21:12:13 UTC  
> Updated at: 2019-03-29 20:29:14 UTC  
> Merged at: 2019-01-27 22:11:04 UTC  
> Closed at: 2019-01-27 22:11:04 UTC  
> Url: https://github.com/boostorg/gil/pull/226  

Remove unused `#include`-s and leave `io/test/paths.hpp` as sole  
place of `#include <boost/filesystem.hpp>` for IO tests.  
  
Silence annoying `-Wconversion` or `-Wshorten-64-to-32` warning  
from Boost.SmartPtr via Boost.Filesystem:  
  
```  
  conversion to 'std::atomic<int>::__integral_type {aka int}'  
    from 'long int' may alter its value  
```  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
  
------  
  
This should disable numerous instances of the warning below thrown during compilation of IO tests  
  
```  
In file included from ../../boost/smart_ptr/detail/atomic_count.hpp:77:0,  
                 from ../../boost/smart_ptr/intrusive_ref_counter.hpp:19,  
                 from ../../boost/filesystem/operations.hpp:32,  
                 from ../../boost/filesystem/convenience.hpp:22,  
                 from io/test/bmp_test.cpp:14:  
../../boost/smart_ptr/detail/atomic_count_std_atomic.hpp: In constructor ‘boost::detail::atomic_count::atomic_count(long int)’:  
../../boost/smart_ptr/detail/atomic_count_std_atomic.hpp:29:48: warning: conversion to ‘std::atomic<int>::__integral_type {aka int}’ from ‘long int’ may alter its value [-Wconversion]  
     explicit atomic_count( long v ): value_( v )  
                                                ^  
```

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-01-27 21:33:04 UTC  
> Url: https://github.com/boostorg/gil/pull/226#issuecomment-457956288  

I've added extra commit to disable the warning for clang

---

## Review 2 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-01-27 21:34:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/226#pullrequestreview-196846538  

Looks still good. :-)

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-01-27 21:36:42 UTC  
> Url: https://github.com/boostorg/gil/pull/226#issuecomment-457956585  

Cheers :) Let's see if it builds.

---
