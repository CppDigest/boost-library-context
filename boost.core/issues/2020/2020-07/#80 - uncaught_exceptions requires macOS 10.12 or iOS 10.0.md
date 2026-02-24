# #80 - uncaught_exceptions requires macOS 10.12 or iOS 10.0 [Closed]

> Username: gjasny  
> Created at: 2020-07-19 18:59:27 UTC  
> Updated at: 2020-07-20 20:16:41 UTC  
> Closed at: 2020-07-20 20:16:41 UTC  
> Url: https://github.com/boostorg/core/issues/80  

Hello,  
  
due to backward compatibility reasons we build boost with `-mmacosx-version-min=10.8`. This results in a compilation error in `boost/core/uncaught_exceptions.hpp` due to unavailability of the API in older macOS versions despite `libc++` announces support. Locally I applied the following patch to fix compilation:  
  
```diff  
diff --git a/boost/core/uncaught_exceptions.hpp b/boost/core/uncaught_exceptions.hpp  
index 27e2491bda..ff71e1925b 100644  
--- a/boost/core/uncaught_exceptions.hpp  
+++ b/boost/core/uncaught_exceptions.hpp  
@@ -32,6 +32,16 @@  
 #define BOOST_CORE_HAS_UNCAUGHT_EXCEPTIONS  
 #endif  
   
+// At least macOS 10.12 or iOS 10.0 is required  
+#if defined(BOOST_CORE_HAS_UNCAUGHT_EXCEPTIONS) && defined(__APPLE__)  
+#   if defined(__MAC_OS_X_VERSION_MIN_REQUIRED) && __MAC_OS_X_VERSION_MIN_REQUIRED < 101200  
+#      undef BOOST_CORE_HAS_UNCAUGHT_EXCEPTIONS  
+#   endif  
+#   if defined(__IPHONE_OS_VERSION_MIN_REQUIRED) && __IPHONE_OS_VERSION_MIN_REQUIRED < 100000  
+#      undef BOOST_CORE_HAS_UNCAUGHT_EXCEPTIONS  
+#   endif  
+#endif  
+  
 #if !defined(BOOST_CORE_HAS_UNCAUGHT_EXCEPTIONS)  
   
 // cxxabi.h availability macro  
```  
  
But I guess Boost already provides a more elegant way to check for availability like a `Boost.Config Feature Check`.  
  
Thanks,  
Gregor

---

## Comment 1

> Username: gjasny  
> Created at: 2020-07-19 19:10:12 UTC  
> Url: https://github.com/boostorg/core/issues/80#issuecomment-660692997  

Availability for watchOS and tvOS (libc++ config.h):  
```  
#  define _LIBCPP_AVAILABILITY_UNCAUGHT_EXCEPTIONS                             \  
     __attribute__((availability(macosx,strict,introduced=10.12)))             \  
     __attribute__((availability(ios,strict,introduced=10.0)))                 \  
     __attribute__((availability(tvos,strict,introduced=10.0)))                \  
     __attribute__((availability(watchos,strict,introduced=3.0)))  
```

---

## Comment 2

> Username: Lastique  
> Created at: 2020-07-19 19:59:06 UTC  
> Url: https://github.com/boostorg/core/issues/80#issuecomment-660699636  

We are using `__cpp_lib_uncaught_exceptions` to detect availability of `std::uncaught_exceptions`. Do I understand correctly that the library defines the macro but does not actually provide the function? If so, please, report the bug to libc++ developers.
