# #15 Fixed type-clash with tag "use_default" from other boost-libs. [Closed]

> Username: DenizThatMenace  
> Created at: 2015-07-16 17:05:01 UTC  
> Updated at: 2015-08-05 11:21:49 UTC  
> Closed at: 2015-07-16 20:12:28 UTC  
> Url: https://github.com/boostorg/iterator/pull/15  

Moved declaration of `use_default` into namespace `boost` instead of declaring it in namespace `boost::iterator` and pulling it into namespace `boost` afterwards via `using`-directive.  
  
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

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-07-16 17:30:07 UTC  
> Url: https://github.com/boostorg/iterator/pull/15#issuecomment-122029643  

The fix is incorrect. Boost.Iterators were intentionally moved to the namespace iterators. The using declarations are provided for backward compatibility. Apparently, Boost.TypeErasure was not updated to accommodate the change.

---

## Comment 2

> Username: DenizThatMenace  
> Created_at: 2015-07-16 20:08:47 UTC  
> Url: https://github.com/boostorg/iterator/pull/15#issuecomment-122073641  

Thanks for the clarification. I created a [pull-request](https://github.com/boostorg/type_erasure/pull/6) for _Boost.Type_Erasure_ instead.

---
