# #27 - Compile as standalone static library [Closed]

> Username: ayounes-synaptics  
> Created at: 2020-01-07 15:30:43 UTC  
> Updated at: 2020-05-02 01:05:36 UTC  
> Closed at: 2020-05-02 01:05:36 UTC  
> Url: https://github.com/boostorg/json/issues/27  

This patch adds a new cmake option to enable compilation of boost::json as a static standalone dependency without any dependency and also compile the examples:  
  
mkdir b && cd b  
cmake .. -DBOOST_JSON_STANDALONE=1  
make  
  
``` diff  
  
diff --git a/CMakeLists.txt b/CMakeLists.txt  
index d0e383e..834e9ce 100644  
--- a/CMakeLists.txt  
+++ b/CMakeLists.txt  
@@ -48,7 +48,13 @@ else()  
     target_compile_definitions(boost_json PUBLIC BOOST_JSON_STATIC_LINK=1)  
 endif()  
   
-if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)  
+option(BOOST_JSON_STANDALONE "Build boost::json as a static standalone library" FALSE)  
+  
+if(BOOST_JSON_STANDALONE)  
+    target_compile_features(boost_json PUBLIC cxx_std_17)  
+    target_compile_definitions(boost_json PUBLIC BOOST_JSON_STANDALONE)  
+    add_subdirectory(example)  
+elseif(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)  
     if(${CMAKE_VERSION} VERSION_LESS 3.16)  
         message(FATAL_ERROR "Boost.JSON development requires CMake 3.16 or newer.")  
     endif()  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-07 16:07:41 UTC  
> Url: https://github.com/boostorg/json/issues/27#issuecomment-571652976  

Could you please submit this as a pull request?

---

## Comment 2

> Username: ayounes-synaptics  
> Created at: 2020-01-08 07:33:26 UTC  
> Url: https://github.com/boostorg/json/issues/27#issuecomment-571926638  

> Could you please submit this as a pull request?  
  
OK, I will try to do that today.

---

## Comment 3

> Username: ayounes-synaptics  
> Created at: 2020-01-08 08:26:54 UTC  
> Url: https://github.com/boostorg/json/issues/27#issuecomment-571942094  

https://github.com/vinniefalco/json/pull/29

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-05-02 01:05:36 UTC  
> Url: https://github.com/boostorg/json/issues/27#issuecomment-622646321  

We went a different route and refactored the CML to support this use-case. It should work now. If you have questions or you encounter problems, please open a new issue!
