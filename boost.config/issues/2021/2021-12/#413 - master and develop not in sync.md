# #413 - master and develop not in sync [Closed]

> Username: pdimov  
> Created at: 2021-12-09 02:01:26 UTC  
> Updated at: 2021-12-09 16:30:44 UTC  
> Closed at: 2021-12-09 11:46:30 UTC  
> Url: https://github.com/boostorg/config/issues/413  

There are a few changes in master that aren't present in develop. I think that these should be merged to develop, to synchronize the two branches.  
  
```  
C:\boost-git\master\libs\config>git diff master..develop  
diff --git a/include/boost/config/assert_cxx98.hpp b/include/boost/config/assert_cxx98.hpp  
deleted file mode 100644  
index aa745d43..00000000  
--- a/include/boost/config/assert_cxx98.hpp  
+++ /dev/null  
@@ -1,23 +0,0 @@  
-//  This file was automatically generated on Wed Mar  3 08:46:11 2021  
-//  by libs/config/tools/generate.cpp  
-//  Copyright John Maddock 2002-4.  
-//  Use, modification and distribution are subject to the  
-//  Boost Software License, Version 1.0. (See accompanying file  
-//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
-  
-//  See http://www.boost.org/libs/config for the most recent version.//  
-//  Revision $Id$  
-//  
-  
-#include <boost/config.hpp>  
-#include <boost/config/assert_cxx17.hpp>  
-  
-#ifdef BOOST_NO_CXX98_BINDERS  
-#  error "Your compiler appears not to be fully C++98 compliant.  Detected via defect macro BOOST_NO_CXX98_BINDERS."  
-#endif  
-#ifdef BOOST_NO_CXX98_FUNCTION_BASE  
-#  error "Your compiler appears not to be fully C++98 compliant.  Detected via defect macro BOOST_NO_CXX98_FUNCTION_BASE."  
-#endif  
-#ifdef BOOST_NO_CXX98_RANDOM_SHUFFLE  
-#  error "Your compiler appears not to be fully C++98 compliant.  Detected via defect macro BOOST_NO_CXX98_RANDOM_SHUFFLE."  
-#endif  
diff --git a/include/boost/config/detail/suffix.hpp b/include/boost/config/detail/suffix.hpp  
index a6e287c7..4533f321 100644  
--- a/include/boost/config/detail/suffix.hpp  
+++ b/include/boost/config/detail/suffix.hpp  
@@ -1210,18 +1210,6 @@ namespace std{ using ::type_info; }  
 #  define BOOST_CXX_VERSION __cplusplus  
 #endif  
  
-//  
-// Define composite agregate macros:  
-//  
-#include <boost/config/detail/cxx_composite.hpp>  
-  
-//  
-// Define the std level that the compiler claims to support:  
-//  
-#ifndef BOOST_CXX_VERSION  
-#  define BOOST_CXX_VERSION __cplusplus  
-#endif  
-  
 //  
 // Finish off with checks for macros that are depricated / no longer supported,  
 // if any of these are set then it's very likely that much of Boost will no  
diff --git a/include/boost/config/stdlib/libstdcpp3.hpp b/include/boost/config/stdlib/libstdcpp3.hpp  
index abcdad54..d0c00155 100644  
--- a/include/boost/config/stdlib/libstdcpp3.hpp  
+++ b/include/boost/config/stdlib/libstdcpp3.hpp  
@@ -184,16 +184,6 @@  
 #endif  
  
  
-#if (BOOST_LIBSTDCXX_VERSION >= 100000) && defined(BOOST_HAS_HASH)  
-//  
-// hash_set/hash_map deprecated and have terminal bugs:  
-//  
-#undef BOOST_HAS_HASH  
-#undef BOOST_HAS_SET_HEADER  
-#undef BOOST_HAS_MAP_HEADER  
-#endif  
-  
-  
 #if (BOOST_LIBSTDCXX_VERSION < 50100)  
 // libstdc++ does not define this function as it's deprecated in C++11, but clang still looks for it,  
 // defining it here is a terrible cludge, but should get things working:  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-09 02:02:29 UTC  
> Url: https://github.com/boostorg/config/issues/413#issuecomment-989432897  

https://github.com/boostorg/config/compare/master

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-12-09 11:46:30 UTC  
> Url: https://github.com/boostorg/config/issues/413#issuecomment-989778504  

That's just plain sloppy, sorry about that, merged.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-12-09 14:15:23 UTC  
> Url: https://github.com/boostorg/config/issues/413#issuecomment-989891721  

Just a small wrinkle here: https://github.com/boostorg/config/blob/f485d71222c5d6a33c0d286f21e6608f1a4a4e7e/include/boost/config/detail/suffix.hpp#L1201-L1223  
  
The merge has resulted in two identical sections, one of which should be deleted.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-12-09 16:30:44 UTC  
> Url: https://github.com/boostorg/config/issues/413#issuecomment-990014288  

Grrr.  Good catch, also sloppy on my part.  Corrected now.  Thanks for spotting that!
