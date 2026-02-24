# #87 - from_time_t is unnecessarily forcing 32-bit seconds [Closed]

> Username: jeking3  
> Created at: 2018-09-20 12:52:45 UTC  
> Updated at: 2022-03-01 16:15:23 UTC  
> Closed at: 2022-03-01 16:15:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/87  

See: https://github.com/boostorg/date_time/pull/35#issuecomment-353632702  
https://github.com/boostorg/date_time/commit/b1dc2b306ea6abb645b0e334e3d5bb1b5ed80818

---

## Comment 1

> Username: jrnphd  
> Created at: 2021-09-15 12:07:36 UTC  
> Updated at: 2021-09-15 12:08:19 UTC  
> Url: https://github.com/boostorg/date_time/issues/87#issuecomment-919960873  

Boost 1.77.0 still has this bug.  The fix appears to be simple enough:  
  
```  
--- boost/date_time/posix_time/conversion.hpp  
+++ boost/date_time/posix_time/conversion.hpp  
@@ -26,7 +26,7 @@  
  inline  
  ptime from_time_t(std::time_t t)  
  {  
-    return ptime(gregorian::date(1970,1,1)) + seconds(static_cast<long>(t));  
+    return ptime(gregorian::date(1970,1,1)) + seconds(t);  
 }  
```
