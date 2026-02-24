# #13 - align: overflow in overflow check [Closed]

> Username: mrahn  
> Created at: 2020-04-27 11:38:14 UTC  
> Updated at: 2020-04-27 14:12:42 UTC  
> Closed at: 2020-04-27 14:12:41 UTC  
> Url: https://github.com/boostorg/align/issues/13  

This code:  
  
```  
#include <boost/align/align.hpp>  
  
int main()  
{  
  auto ptr ((void*)140665412970093);  
  std::size_t space (211);  
  
  if (boost::alignment::align (1024, 195, ptr, space) != nullptr)  
  {  
    std::abort();  
  }  
}  
```  
  
aborts when compiled using `-DBOOST_NO_CXX11_STD_ALIGN`.  
  
This diff  
  
```  
diff --git a/include/boost/align/detail/align.hpp b/include/boost/align/detail/align.hpp  
index 905940a..0483c55 100644  
--- a/include/boost/align/detail/align.hpp  
+++ b/include/boost/align/detail/align.hpp  
@@ -22,8 +22,9 @@ align(std::size_t alignment, std::size_t size, void*& ptr,  
     if (size <= space) {  
         char* p = reinterpret_cast<char*>(~(alignment - 1) &  
             (reinterpret_cast<std::size_t>(ptr) + alignment - 1));  
-        std::size_t n = space - (p - static_cast<char*>(ptr));  
-        if (size <= n) {  
+        std::size_t d = p - static_cast<char*>(ptr);  
+        std::size_t n = space - d;  
+        if (d <= space && size <= n) {  
             ptr = p;  
             space = n;  
             return p;  
```  
  
deals with the case that the pointer after alignment is already outside the available space.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-04-27 14:12:41 UTC  
> Url: https://github.com/boostorg/align/issues/13#issuecomment-620013503  

Thanks for reporting it. Fixed a little differently in 6e30998.
