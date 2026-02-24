# #397 Fix undefined reference to ws2_32 [Merged]

> Username: jschueller  
> Created at: 2024-08-31 07:33:37 UTC  
> Updated at: 2024-12-14 05:14:37 UTC  
> Merged at: 2024-10-24 22:53:11 UTC  
> Closed at: 2024-10-24 22:53:11 UTC  
> Url: https://github.com/boostorg/process/pull/397  

else it can fail to link on mingw:  
```  
process_handle_windows.o:process_handle_windows.cpp:(.text+0x25): undefined reference to `_imp__WSACleanup@0'  
```  
  
  
/cc @klemens-morgenstern

---

## Comment 1

> Username: MehdiChinoune  
> Created_at: 2024-12-14 05:14:36 UTC  
> Url: https://github.com/boostorg/process/pull/397#issuecomment-2542825573  

a similar fix is required for CMake.  
```diff  
--- a/CMakeLists.txt  
+++ b/CMakeLists.txt  
@@ -57,7 +57,7 @@  
 endif()  
   
 if (WIN32)  
-  target_link_libraries(boost_process PUBLIC ntdll shell32 advapi32 user32)  
+  target_link_libraries(boost_process PUBLIC ntdll shell32 advapi32 user32 ws2_32)  
 endif()  
   
 if(BUILD_SHARED_LIBS)  
```

---
