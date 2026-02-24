# #45 - missing boost/config.hpp includes [Closed]

> Username: urnathan  
> Created at: 2022-09-14 11:50:09 UTC  
> Updated at: 2022-11-26 00:42:46 UTC  
> Closed at: 2022-11-26 00:42:46 UTC  
> Url: https://github.com/boostorg/exception/issues/45  

Some of the exception headers examine boost control macros without first including config.hpp.  Thus, they are not self-contained, and that creates difficulty building them as clang modules or c++ header units (specifically they think the macros are undefined, which might mismatch the rest of the build, leading to conflicts).  Here are the ones I found problematic (I think there are others, particularly WRT BOOST_EXCEPTION_ENABLE_WARNINGS, but that doesn't bite in this case.  
  
```  
diff --git a/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/current_exception_cast.hpp b/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/current_exception_cast.hpp  
--- a/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/current_exception_cast.hpp  
+++ b/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/current_exception_cast.hpp  
@@ -6,6 +6,8 @@  
 #ifndef BOOST_EXCEPTION_7E83C166200811DE885E826156D89593  
 #define BOOST_EXCEPTION_7E83C166200811DE885E826156D89593  
   
+#include <boost/config.hpp>  
+  
 #ifndef BOOST_EXCEPTION_ENABLE_WARNINGS  
 #if __GNUC__*100+__GNUC_MINOR__>301  
 #pragma GCC system_header  
diff --git a/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/is_output_streamable.hpp b/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/is_output_streamable.hpp  
--- a/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/is_output_streamable.hpp  
+++ b/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/is_output_streamable.hpp  
@@ -6,6 +6,7 @@  
 #ifndef BOOST_EXCEPTION_898984B4076411DD973EDFA055D89593  
 #define BOOST_EXCEPTION_898984B4076411DD973EDFA055D89593  
   
+#include <boost/config.hpp>  
 #include <ostream>  
   
 #ifndef BOOST_EXCEPTION_ENABLE_WARNINGS  
diff --git a/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/shared_ptr.hpp b/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/shared_ptr.hpp  
--- a/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/shared_ptr.hpp  
+++ b/third-party/tp2/boost/1.77.0/boost_1_77_0/boost/exception/detail/shared_ptr.hpp  
@@ -6,6 +6,8 @@  
 #ifndef BOOST_EXCEPTION_837060E885AF11E68DA91D15E31AC075  
 #define BOOST_EXCEPTION_837060E885AF11E68DA91D15E31AC075  
   
+#include <boost/config.hpp>  
+  
 #ifdef BOOST_EXCEPTION_MINI_BOOST  
 #include  <memory>  
 namespace boost { namespace exception_detail { using std::shared_ptr; } }  
```

---

## Comment 1

> Username: zajo  
> Created at: 2022-11-14 07:00:45 UTC  
> Url: https://github.com/boostorg/exception/issues/45#issuecomment-1313181807  

I see that clone_current_exception.hpp uses BOOST_NO_EXCEPTIONS without #include <boost/config.hpp>, which is a bug, but I think there aren't any others. I'll fix this one, but if there is another case where such macro is used without the include, please let me know.
