# #3003 - Missing #include <cstdint> [Closed]

> Username: yliu1021  
> Created at: 2025-04-25 06:52:44 UTC  
> Updated at: 2025-04-26 19:21:29 UTC  
> Closed at: 2025-04-26 19:21:29 UTC  
> Url: https://github.com/boostorg/beast/issues/3003  

In `include/boost/beast/core/detail/cpu_info.hpp`, the use of `std::uint32_t` fails to compile on gcc-14 because the header `cstdint` is not included.  
  
I encountered this bug trying to build a Bazel project using boost.beast (https://registry.bazel.build/modules/boost.beast). Applying the following patch fixed the issue:  
```  
--- a/include/boost/beast/core/detail/cpu_info.hpp	2025-04-25 06:37:19.059710117 +0000  
+++ b/include/boost/beast/core/detail/cpu_info.hpp	2025-04-25 06:37:44.003876693 +0000  
@@ -11,6 +11,7 @@  
 #define BOOST_BEAST_DETAIL_CPU_INFO_HPP  
   
 #include <boost/config.hpp>  
+#include <cstdint>  
   
 #ifndef BOOST_BEAST_NO_INTRINSICS  
 # if defined(BOOST_MSVC) || ((defined(BOOST_GCC) || defined(BOOST_CLANG)) && defined(__SSE4_2__))  
```
