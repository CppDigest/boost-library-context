# #777 - No default for BOOST_URL_TEST_FLAGS [Closed]

> Username: aminiussi  
> Created at: 2023-08-02 09:41:00 UTC  
> Updated at: 2023-10-06 16:02:13 UTC  
> Closed at: 2023-10-06 16:02:13 UTC  
> Url: https://github.com/boostorg/url/issues/777  

Hi,  
  
There is no default value for BOOST_URL_TEST_FLAGS in  libs/url/test/CMakeLists.txt.  
  
I suspect that's the reason why an unanticipated compiler will cause a failure  
```  
[...]  
CMake Error at libs/url/test/unit/CMakeLists.txt:19 (set_source_files_properties):  
  set_source_files_properties called with incorrect number of arguments.  
[...]  
```  
 in libs/url/test/CMakeLists.txt.:19:  
```  
set_source_files_properties(${BOOST_URL_TESTS_FILES} PROPERTIES COMPILE_FLAGS ${BOOST_URL_TEST_FLAGS})  
```

---

## Comment 1

> Username: aminiussi  
> Created at: 2023-08-02 10:43:48 UTC  
> Url: https://github.com/boostorg/url/issues/777#issuecomment-1661979602  

Also, **-Werror** is probably not viable for new Intel's oneAPI.  
  
Here is a patch that should fix both issues:  
```  
12:38:41 [alainm@castor url]# git diff -c  
diff --git a/test/CMakeLists.txt b/test/CMakeLists.txt  
index 868ebbb..b1acc5f 100644  
--- a/test/CMakeLists.txt  
+++ b/test/CMakeLists.txt  
@@ -29,8 +29,18 @@ elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang" AND CMAKE_CXX_COMPILER_VERSION_MAJ  
     set(BOOST_URL_TEST_FLAGS "-Wall -Werror -Wno-unused-but-set-variable")  
 elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")  
     set(BOOST_URL_TEST_FLAGS "-Wall -Werror")  
+elseif (CMAKE_CXX_COMPILER_ID MATCHES "Intel")  
+    if (CMAKE_CXX_COMPILER_ID STREQUAL "IntelLLVM")  
+        # Too many unknown legacy Intel options are forced onto  
+        # oneAPI to use -Werror  
+        set(BOOST_URL_TEST_FLAGS "-Wall")  
+    else ()  
+        set(BOOST_URL_TEST_FLAGS "-Wall -Werror")  
+    endif()  
 elseif (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")  
     set(BOOST_URL_TEST_FLAGS "/W4 /WX")  
+else()  
+    message(WARNING "Unsupported compiler id: ${CMAKE_CXX_COMPILER_ID}")  
 endif()  
   
 set(SUITE_FILES  
12:38:43 [alainm@castor url]#   
  
```

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-08-02 12:42:20 UTC  
> Url: https://github.com/boostorg/url/issues/777#issuecomment-1662141332  

> There is no default value for BOOST_URL_TEST_FLAGS in libs/url/test/CMakeLists.txt.  
  
The flags are defined here:  
  
https://github.com/boostorg/url/blob/a1181275d02a0a4c6ab8147354f752ec36e1dd98/test/CMakeLists.txt#L24-L34  
  
but yes, some unsupported compiler would unfortunately give us  
  
```cmake  
  set_source_files_properties called with incorrect number of arguments.  
```  
  
instead of setting the flags to an empty string.  
  
We need a default empty flag at the start.  
  
```cmake  
set(BOOST_URL_TEST_FLAGS "")  
if (...)  
...  
```  
  
This solution:  
  
```cmake  
message(WARNING "Unsupported compiler id: ${CMAKE_CXX_COMPILER_ID}")  
```  
  
wouldn't prevent the script from reaching the   
  
https://github.com/boostorg/url/blob/a1181275d02a0a4c6ab8147354f752ec36e1dd98/test/unit/CMakeLists.txt#L19  
  
command though.  
  
The thing about unsupported compilers is that we don't test them, and when we do, they're no longer unsupported.  
  
In the meanwhile you can check if   
  
```  
set(BOOST_URL_TEST_FLAGS "")  
```  
  
before the conditionals work for you.

---

## Comment 3

> Username: aminiussi  
> Created at: 2023-08-02 14:34:41 UTC  
> Url: https://github.com/boostorg/url/issues/777#issuecomment-1662327837  

Adding  
```  
set(BOOST_URL_TEST_FLAGS "")  
```  
before the flag setting section works for me.  
  
The warning (that could be turned into a fatal if the lack of warning in tests is considered an important issue, as suggested by the -Werror) tells the user that maybe something should be done tough.  
  
The two point are independent though.    
  
My main concern is to have **Intel** and **IntelLLVM** dealt with.  
  
Thanks
