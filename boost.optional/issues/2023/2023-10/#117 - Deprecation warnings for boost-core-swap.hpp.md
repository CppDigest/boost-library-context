# #117 - Deprecation warnings for boost/core/swap.hpp [Closed]

> Username: jefftrull  
> Created at: 2023-10-23 21:40:29 UTC  
> Updated at: 2023-10-23 22:20:05 UTC  
> Closed at: 2023-10-23 22:20:05 UTC  
> Url: https://github.com/boostorg/optional/issues/117  

It seems that swap has recently made changes to the include path and `boost/core/swap.hpp` is no longer recommended. This is causing deprecation warnings in many places since optional is used all over. The following changes seem to fix the problem:  
  
```  
--- a/include/boost/optional/detail/optional_swap.hpp  
+++ b/include/boost/optional/detail/optional_swap.hpp  
@@ -13,7 +13,7 @@  
 #ifndef BOOST_OPTIONAL_DETAIL_OPTIONAL_SWAP_AJK_28JAN2015_HPP  
 #define BOOST_OPTIONAL_DETAIL_OPTIONAL_SWAP_AJK_28JAN2015_HPP  
   
-#include <boost/core/swap.hpp>  
+#include <boost/core/invoke_swap.hpp>  
 #include <boost/optional/optional_fwd.hpp>  
   
 namespace boost {  
diff --git a/include/boost/optional/optional.hpp b/include/boost/optional/optional.hpp  
index 56d50d7..6e6e10e 100644  
--- a/include/boost/optional/optional.hpp  
+++ b/include/boost/optional/optional.hpp  
@@ -30,7 +30,7 @@  
 #include <boost/core/addressof.hpp>  
 #include <boost/core/enable_if.hpp>  
 #include <boost/core/explicit_operator_bool.hpp>  
-#include <boost/core/swap.hpp>  
+#include <boost/core/invoke_swap.hpp>  
 #include <boost/optional/bad_optional_access.hpp>  
 #include <boost/static_assert.hpp>  
 #include <boost/throw_exception.hpp>  
```

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-10-23 21:48:29 UTC  
> Url: https://github.com/boostorg/optional/issues/117#issuecomment-1776075281  

Thanks for the report. The changes you request have beenalready applied in branch `develop`.
