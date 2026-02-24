# #8 Remove deprecated includes [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-13 19:52:16 UTC  
> Updated at: 2015-09-17 12:57:43 UTC  
> Merged at: 2015-09-17 12:24:36 UTC  
> Closed at: 2015-09-17 12:24:36 UTC  
> Url: https://github.com/boostorg/concept_check/pull/8  

A comment in boost/iterator.hpp and boost/detail/iterator.hpp mentions that the files are obsolete and will be deprecated. All they do is pull some types from namespace std into namespace boost. boost/iterator.hpp isn't used anyway.  
  
Tested with MSVC 14.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-09-14 14:13:03 UTC  
> Url: https://github.com/boostorg/concept_check/pull/8#issuecomment-140089751  

This looks OK to me.

---
