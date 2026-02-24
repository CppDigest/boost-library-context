# #225 - Fix boost (>= 1.66) on gcc 4.9.3 [Open]

> Username: muffgaga  
> Created at: 2018-05-02 11:32:06 UTC  
> Updated at: 2018-08-03 17:13:35 UTC  
> Url: https://github.com/boostorg/config/issues/225  

When trying to use boost 1.66 on gcc 4.9.3 (not sure about 4.9.4; we use gcc 4.9.3 as it is the last "known good" version for `gccxml`) we encountered a strange bug related to gcc's handling of `__has_include`:  
The macro is defined but not implemented correctly.  
Our workaround was to conditionally undefine the macro when encountering gcc 4.9.x.  
  
```diff  
diff -rpu boost_1_66_0_old/boost/config/stdlib/libstdcpp3.hpp boost_1_66_0/boost/config/stdlib/libstdcpp3.hpp  
--- boost_1_66_0_old/boost/config/stdlib/libstdcpp3.hpp	2017-12-14 00:56:42.000000000 +0100  
+++ boost_1_66_0/boost/config/stdlib/libstdcpp3.hpp	2018-04-25 16:42:42.627694409 +0200  
@@ -301,6 +301,11 @@ extern "C" char *gets (char *__s);  
 #  define BOOST_NO_CXX17_STD_APPLY  
 #endif  
   
+#if defined(__GNUC__) && (__GNUC__ == 4) && (__GNUC_MINOR__ == 9) && defined(__has_include)  
+// gcc 4.9.x defines but does not implement it  
+#undef __has_include  
+#endif  
+  
 #if defined(__has_include)  
 #if !__has_include(<shared_mutex>)  
 #  define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-03 17:13:34 UTC  
> Url: https://github.com/boostorg/config/issues/225#issuecomment-410318626  

That's nasty, but the fix looks almost as bad.... not sure what to do about this one.
