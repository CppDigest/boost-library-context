# #143 - -fsplit-stack flag and AIX [Closed]

> Username: EGuesnet  
> Created at: 2020-06-23 10:32:22 UTC  
> Updated at: 2020-06-26 05:15:39 UTC  
> Closed at: 2020-06-26 05:15:39 UTC  
> Url: https://github.com/boostorg/context/issues/143  

Hello,  
I am trying to port Boost on AIX.  
  
OS: AIX 7.1  
Compiler: GCC 8.4  
  
The context library and test-suite use the -split-stack flag. However, according GCC documentation (https://gcc.gnu.org/onlinedocs/gcc-7.3.0/gcc/Instrumentation-Options.html), this flag is only available on Linux x86. According error message I have on AIX,  
  
```  
cc1plus: error: '-fsplit-stack' currently only supported on PowerPC64 GNU/Linux with glibc-2.18 or later  
cc1plus: error: '-fsplit-stack' is not supported by this compiler configuration  
```  
  
this flag is also available on Linux PowerPC64. So, it is not available on AIX, and it is probably the same on some other systems.  
  
Without this flag, library can be build and most of the context test-suite pass on AIX.  
  
I have considered in our build as a workaround to add this flag only on Linux:  
  
```diff  
--- libs/context/test/Jamfile.v2.orig   2020-06-23 12:06:37 -0500  
+++ libs/context/test/Jamfile.v2        2020-06-16 16:18:37 -0500  
@@ -18,8 +18,8 @@  
     : requirements  
       <library>../../test/build//boost_unit_test_framework  
       <library>/boost/context//boost_context  
-      <toolset>gcc,<segmented-stacks>on:<cxxflags>-fsplit-stack  
-      <toolset>gcc,<segmented-stacks>on:<cxxflags>-DBOOST_USE_SEGMENTED_STACKS  
+      <target-os>linux,<toolset>gcc,<segmented-stacks>on:<cxxflags>-fsplit-stack  
+      <target-os>linux,<toolset>gcc,<segmented-stacks>on:<cxxflags>-DBOOST_USE_SEGMENTED_STACKS  
       <toolset>clang,<segmented-stacks>on:<cxxflags>-fsplit-stack  
       <toolset>clang,<segmented-stacks>on:<cxxflags>-DBOOST_USE_SEGMENTED_STACKS  
       <link>static  
```  
  
And the same on `libs/context/build/Jamfile.v2` file.  
  
For your information, this issue is also present for the coroutine and fiber libs.

---

## Comment 1

> Username: olk  
> Created at: 2020-06-23 17:23:35 UTC  
> Url: https://github.com/boostorg/context/issues/143#issuecomment-648303005  

Could you create a pull-request pelase?
