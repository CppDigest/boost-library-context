# #14 Fix narrowing conversion in tuple_manipulator constructor [Merged]

> Username: Lastique  
> Created at: 2019-09-15 12:42:06 UTC  
> Updated at: 2019-09-16 06:05:34 UTC  
> Merged at: 2019-09-16 00:03:53 UTC  
> Closed at: 2019-09-16 00:03:53 UTC  
> Url: https://github.com/boostorg/tuple/pull/14  

This fixes unnecessary, possibly wrapping conversion of the character argument of tuple manipulators on `tuple_manipulator` construction. This should silence MSVC warning C4244:  
  
```  
tuple_io.hpp(142,71): warning C4244:  'argument': conversion from 'const CharType' to 'const char', possible loss of data  
```  
  
Related to https://github.com/boostorg/filesystem/issues/118.

---
