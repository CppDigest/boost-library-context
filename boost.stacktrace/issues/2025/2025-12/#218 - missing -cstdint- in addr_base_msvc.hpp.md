# #218 - missing <cstdint> in addr_base_msvc.hpp [Closed]

> Username: akioweh  
> Created at: 2025-12-10 21:24:23 UTC  
> Updated at: 2025-12-23 17:34:06 UTC  
> Closed at: 2025-12-23 17:34:06 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/218  

the following patch seems necessary to build correctly on non-MSVC windows toolchains.  
  
```diff  
diff --git a/libs/stacktrace/include/boost/stacktrace/detail/addr_base_msvc.hpp b/libs/stacktrace/include/boost/stacktrace/detail/addr_base_msvc.hpp  
index d179896..9b87c5f 100644  
--- a/libs/stacktrace/include/boost/stacktrace/detail/addr_base_msvc.hpp  
+++ b/libs/stacktrace/include/boost/stacktrace/detail/addr_base_msvc.hpp  
@@ -18,12 +18,14 @@  
 #ifdef WIN32_LEAN_AND_MEAN  
 #include <windows.h>  
 #include <psapi.h>  
+#include <cstdint>  
 #else  
 // Prevent inclusion of extra Windows SDK headers which can cause conflict  
 // with other code using Windows SDK  
 #define WIN32_LEAN_AND_MEAN  
 #include <windows.h>  
 #include <psapi.h>  
+#include <cstdint>  
 #undef WIN32_LEAN_AND_MEAN  
 #endif  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-12-23 17:34:06 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/218#issuecomment-3687440303  

Many thanks for the report!  
  
This issue was fixed in https://github.com/boostorg/stacktrace/commit/07bc960cfead55ca3f4a970f66f3980cd95ee128  
  
Boost 1.90 release contains the fix, please update to that version
