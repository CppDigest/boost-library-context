# #27 - header include cycle problematic for modules [Closed]

> Username: urnathan  
> Created at: 2022-09-13 11:45:05 UTC  
> Updated at: 2022-10-27 11:48:36 UTC  
> Closed at: 2022-10-27 11:48:36 UTC  
> Url: https://github.com/boostorg/container_hash/issues/27  

There is a cycle between container_hash/hash.hpp and container_hash/extensions.hpp.  This is problematic for modules (both clang and C++ header units).  This cycle is made acceptable by removing extensions.hpp's idempotency and have it pre-include hash.hpp if that's not already been included.  Then, should hash.hpp include extensions.hpp the later conditional will nullify the (second) tokenization of extensions.hpp's body.  This preserves the existing textual-header behaviour of the two files. But, it also makes them buildable as clang-headers, because the cycality remaining in the graph is unproblematic.  
  
An alternative would be to move the body of extensions.hpp to extensions-impl.hpp, and have hash.hpp conditionally include that.  Then turn extensions.hpp into a stub that include hash.hpp with BOOST_HASH_NO_EXTENSIONS undefined so it includes extensions-impl.  that way extensions-impl.hpp and extensions.hpp can be idempotent (but hash.hpp has to remain not)  
  
```  
+++ boost/container_hash/extensions.hpp  
@@ -10,15 +10,22 @@  
 // This implements the extensions to the standard.  
 // It's undocumented, so you shouldn't use it....  
   
+#if !defined(BOOST_FUNCTIONAL_HASH_HASH_HPP)  
+// container_hash/hash.hpp and container_hash/extensions.hpp  
+// (conditionally) include each other. This causes problems for clang  
+// modules (and c++ header units). So, we include  
+// container_hash/hash.hpp *outside our header protection* if it's not  
+// already included. That in turn might reinclude us, in which case  
+// when we get back to here, our header protection will kick in. When  
+// building as a module, the cycle is unproblematic.  
+#include <boost/container_hash/hash.hpp>  
+#endif  
+  
 #if !defined(BOOST_FUNCTIONAL_HASH_EXTENSIONS_HPP)  
 #define BOOST_FUNCTIONAL_HASH_EXTENSIONS_HPP  
   
 #include <boost/config.hpp>  
-#if defined(BOOST_HAS_PRAGMA_ONCE)  
-#pragma once  
-#endif  
   
-#include <boost/container_hash/hash.hpp>  
 #include <boost/detail/container_fwd.hpp>  
 #include <boost/core/enable_if.hpp>  
 #include <boost/static_assert.hpp>  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-09-13 12:17:58 UTC  
> Url: https://github.com/boostorg/container_hash/issues/27#issuecomment-1245331193  

This will be taken care of when I merge the develop branch, but it's not ready for prime time yet. I'll try to get that in before 1.81.

---

## Comment 2

> Username: urnathan  
> Created at: 2022-09-14 18:30:52 UTC  
> Url: https://github.com/boostorg/container_hash/issues/27#issuecomment-1247152296  

Thanks Peter!

---

## Comment 3

> Username: pdimov  
> Created at: 2022-10-27 11:48:36 UTC  
> Url: https://github.com/boostorg/container_hash/issues/27#issuecomment-1293408206  

The develop branch has been merged now, so this should be resolved.
