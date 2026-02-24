# #164 - `-Wconversion` warning in `type_traits/is_complete.hpp` [Closed]

> Username: cmazakas  
> Created at: 2022-02-08 19:08:25 UTC  
> Updated at: 2022-10-31 15:43:08 UTC  
> Closed at: 2022-10-31 15:43:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/164  

```  
gcc.compile.c++ ../../bin.v2/libs/unordered/test/bucket_tests.test/gcc-10/debug/cxxstd-20-iso/threading-multi/visibility-hidden/unordered/bucket_tests.o  
In file included from ../../boost/type_traits/is_convertible.hpp:17,  
                 from ../../boost/unordered/detail/implementation.hpp:40,  
                 from ../../boost/unordered/detail/set.hpp:6,  
                 from ../../boost/unordered/unordered_set.hpp:20,  
                 from ../../boost/unordered_set.hpp:17,  
                 from test/unordered/bucket_tests.cpp:8:  
../../boost/type_traits/is_complete.hpp:47:14: warning: conversion from ‘long unsigned int’ to ‘unsigned int’ may change value [-Wconversion]  
   47 |       ok_tag<sizeof(T)> check_is_complete(int);  
      |              ^~~~~~~~~  
```  
  
It seems like there's a conversion happening here and it generates a warning.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-30 00:13:47 UTC  
> Url: https://github.com/boostorg/type_traits/issues/164#issuecomment-1296032268  

This seems to have been fixed by #163.
