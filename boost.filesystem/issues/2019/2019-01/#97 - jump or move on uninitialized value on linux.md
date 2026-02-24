# #97 - jump or move on uninitialized value on linux [Closed]

> Username: mccann  
> Created at: 2019-01-05 02:29:49 UTC  
> Updated at: 2019-07-30 11:19:20 UTC  
> Closed at: 2019-07-30 11:19:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/97  

Upgraded from boost 1.67 to 1.69, started getting valgrind errors related to jump or move on uninitialized value.   
  
Triggered by this line:  
```if (entry->d_type == DT_UNKNOWN) // filesystem does not supply d_type value```  
https://github.com/boostorg/filesystem/blob/boost-1.69.0/src/operations.cpp#L2163  
  
From what I can tell, this was introduced with this commit: https://github.com/boostorg/filesystem/commit/48b8d753dd7bac2bd4466174fd403aaf4b01def6  
  
I believe what is happening (on my system), `BOOST_FILESYSTEM_STATUS_CACHE` is defined, and yet `::readdir_r()` is not being called anymore after https://github.com/boostorg/filesystem/commit/48b8d753dd7bac2bd4466174fd403aaf4b01def6 , which leaves the `d_type` member of the dirent struct uninitialized.   
  
I have used the following patch to address this.  
```diff --git a/libs/filesystem/src/operations.cpp b/libs/filesystem/src/operations.cpp  
index 53dcdb7..0749f91 100644  
--- a/libs/filesystem/src/operations.cpp  
+++ b/libs/filesystem/src/operations.cpp  
@@ -2144,6 +2144,9 @@ namespace  
       return errno;  
     std::strcpy(entry->d_name, p->d_name);  
     *result = entry;  
+#   ifdef BOOST_FILESYSTEM_STATUS_CACHE  
+    entry->d_type = DT_UNKNOWN;  
+#   endif  
     return 0;  
   }  
 ```

---

## Comment 1

> Username: Lastique  
> Created at: 2019-07-30 11:19:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/97#issuecomment-516376715  

This should be fixed after bbe9d1771e5d679b3f10c42a58fc81f7e8c024a9.
