# #6 Moved "use_default" from namespace "boost" into "boost/type_erasure". [Closed]

> Username: DenizThatMenace  
> Created at: 2015-07-16 20:06:45 UTC  
> Updated at: 2015-08-05 11:00:10 UTC  
> Closed at: 2015-08-05 10:59:50 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/6  

This fixes compiler-errors ("conflicting declaration") which would occur if included in the same translation-unit as "boost/iterator/iterator_adaptor.hpp".  
  
The clash with the original code would occur for the following simple code, for example:  
  
```  
#include <boost/type_erasure/iterator.hpp>  
#include <boost/iterator/iterator_adaptor.hpp>  
```  
  
Both, GCC and Clang fail to compile this, but Clang is more verbose:  
  
```  
/tmp/boost-1_58/boost/iterator/iterator_adaptor.hpp:44:18: error: target of using declaration conflicts with declaration already in scope  
using iterators::use_default;  
                 ^  
/tmp/boost-1_58/boost/iterator/iterator_adaptor.hpp:40:10: note: target of using declaration  
  struct use_default;  
         ^  
/tmp/boost-1_58/boost/type_erasure/iterator.hpp:27:8: note: conflicting declaration  
struct use_default;  
       ^  
1 error generated.  
```  
  
I originally provided a patch against _Boost.Iterator_, however, I [got told](https://github.com/boostorg/iterator/pull/15#issuecomment-122029643) that patch should be applied to _Boost.Type_Erasure_ instead.

---

## Comment 1

> Username: DenizThatMenace  
> Created_at: 2015-08-05 10:59:50 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/6#issuecomment-127955398  

It seems this has been fixed on develop in the meanwhile. So I am just withdrawing/closing this pull-request.  
Thanks for the fix.

---
