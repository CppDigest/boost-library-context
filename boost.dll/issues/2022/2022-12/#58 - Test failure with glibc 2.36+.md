# #58 - Test failure with glibc 2.36+ [Closed]

> Username: sgn  
> Created at: 2022-12-15 11:48:40 UTC  
> Updated at: 2023-05-29 05:58:36 UTC  
> Closed at: 2023-05-29 05:58:36 UTC  
> Url: https://github.com/boostorg/dll/issues/58  

In glibc 2.36 and later, `librt.so` has been merged into `libc` ([a similar blog for libpthread](https://developers.redhat.com/articles/2021/12/17/why-glibc-234-removed-libpthread#remaining_issues), the test of Boost.Dll tries to `dlopen(2)` `libdl.so` will run into failure because it's not existed.  
  
Since zlib is a dependency of Boost, can we change to that:  
```diff  
glibc 2.36 doesn't have libdl.so, it has been melted into libc  
Change to libz.so  
Index: boost-1.81.0/libs/dll/test/shared_library_load_test.cpp  
===================================================================  
--- boost-1.81.0.orig/libs/dll/test/shared_library_load_test.cpp  
+++ boost-1.81.0/libs/dll/test/shared_library_load_test.cpp  
@@ -231,7 +231,7 @@ int main(int argc, char* argv[])  
             boost::dll::shared_library("winmm.dll");  
             BOOST_TEST(false);  
 #elif BOOST_OS_LINUX  
-            boost::dll::shared_library("libdl.so");  
+            boost::dll::shared_library("libz.so");  
             BOOST_TEST(false);  
 #endif  
         } catch (...) {}  
@@ -242,7 +242,7 @@ int main(int argc, char* argv[])  
 #if BOOST_OS_WINDOWS  
             boost::dll::shared_library("winmm", load_mode::search_system_folders | load_mode::append_decorations);  
 #elif BOOST_OS_LINUX  
-            boost::dll::shared_library("dl", boost::dll::load_mode::search_system_folders | load_mode::append_decorations);  
+            boost::dll::shared_library("z", boost::dll::load_mode::search_system_folders | load_mode::append_decorations);  
 #endif  
         } catch (...) {  
             BOOST_TEST(false);  
```
