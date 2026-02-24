# #209 - Y2K38 Bug:  from_time_t is unnecessarily forcing 32-bit seconds [Closed]

> Username: jrnphd  
> Created at: 2022-04-27 13:14:01 UTC  
> Updated at: 2022-04-28 00:54:05 UTC  
> Closed at: 2022-04-28 00:54:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/209  

The bug reported in https://github.com/boostorg/date_time/issues/87 is still present in boost version 1.79.0  
  
The fix is trivial:  
  
`--- boost/date_time/posix_time/conversion.hpp  
+++ boost/date_time/posix_time/conversion.hpp  
@@ -26,7 +26,7 @@  
  inline  
  ptime from_time_t(std::time_t t)  
  {  
-    return ptime(gregorian::date(1970,1,1)) + seconds(static_cast<long>(t));  
+    return ptime(gregorian::date(1970,1,1)) + seconds(t);  
 }   
`

---

## Comment 1

> Username: jeking3  
> Created at: 2022-04-28 00:54:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/209#issuecomment-1111621697  

This is fixed by #80 which did not make it into Boost 1.79 - it will be in Boost 1.80
