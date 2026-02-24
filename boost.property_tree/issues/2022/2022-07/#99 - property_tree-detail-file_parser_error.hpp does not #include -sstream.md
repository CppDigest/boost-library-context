# #99 - property_tree/detail/file_parser_error.hpp does not #include <sstream> [Closed]

> Username: urnathan  
> Created at: 2022-07-12 13:24:18 UTC  
> Updated at: 2023-12-01 07:40:55 UTC  
> Closed at: 2023-12-01 07:40:55 UTC  
> Url: https://github.com/boostorg/property_tree/issues/99  

This was discovered when enabling clang implicit modules (and by implication will affect C++20 header-units).  property_tree/detail/file_parser_error.hpp uses stringstream without #including <sstream>, and therefore is not self contained.  Fixed thusly:  
```  
--- c/1.77.0/src/boost_1_77_0/boost/property_tree/detail/file_parser_error.hpp  
+++ w/1.77.0/src/boost_1_77_0/boost/property_tree/detail/file_parser_error.hpp  
@@ -12,6 +12,7 @@  
   
 #include <boost/property_tree/ptree.hpp>  
 #include <string>  
+#include <sstream>  
   
 namespace boost { namespace property_tree  
 {  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-07-12 21:20:56 UTC  
> Url: https://github.com/boostorg/property_tree/issues/99#issuecomment-1182511511  

Thank you.

---

## Comment 2

> Username: ashtum  
> Created at: 2023-12-01 07:40:55 UTC  
> Url: https://github.com/boostorg/property_tree/issues/99#issuecomment-1835621754  

Addressed in https://github.com/boostorg/property_tree/pull/112.
