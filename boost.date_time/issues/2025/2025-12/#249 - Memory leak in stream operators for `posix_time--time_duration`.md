# #249 - Memory leak in stream operators for `posix_time::time_duration` [Open]

> Username: belyaev-ms  
> Created at: 2025-12-18 13:31:32 UTC  
> Updated at: 2025-12-18 13:31:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/249  

## Memory leak in stream operators for `posix_time::time_duration`  
  
The `<<` and `>>` operators for `boost::posix_time::time_duration` may cause memory leaks when an exception is thrown during memory allocation within these operators.  
  
**Steps to reproduce:**  
  
The following example simulates a memory allocation failure during the stream output operation for a `time_duration` variable. Custom `new`/`delete` operators are overloaded to force an exception on the 8th allocation call. The example should be built with AddressSanitizer (ASAN) to confirm the leak.  
  
```cpp  
#include <vector>  
#include <string>  
#include <iostream>  
#include <stdint.h>  
#include <atomic>  
#include <boost/date_time/posix_time/posix_time.hpp>  
  
static std::atomic<int> fault_counter(-1);  
  
void* operator new(std::size_t size)  
{  
    if (0 == size)  
    {  
        ++size;  
    }  
  
    if (fault_counter == -1 || fault_counter++ != 8)  
    {  
        void* ptr = malloc(size);  
        if (ptr)  
        {  
            printf("[+] %p size=%lu\n", ptr, size);  
            return ptr;  
        }  
    }  
    printf("[!] FAULT size=%lu\n", size);  
    throw std::bad_alloc{};  
}  
  
void operator delete(void* ptr) noexcept  
{  
    printf("[-] %p\n", ptr);  
    std::free(ptr);  
}  
  
void operator delete(void* ptr, std::size_t ) noexcept  
{  
    printf("[-] %p\n", ptr);  
    std::free(ptr);  
}  
  
int main(int , char** )  
{  
    try  
    {  
        puts("---------------------------------------");  
        fault_counter = 0;  
        boost::posix_time::time_duration td = boost::posix_time::seconds(60) + boost::posix_time::microsec(1000);  
        std::ostringstream ss;  
        ss << td;  
        puts("---------------------------------------");  
    }  
    catch (...)  
    {  
  
    }  
    return 0;  
}  
```  
  
**Live demonstration:**    
https://godbolt.org/z/zWo4Mdeac  
  
**Result (ASAN report):**  
```  
=================================================================  
==1==ERROR: LeakSanitizer: detected memory leaks  
  
Direct leak of 424 byte(s) in 1 object(s) allocated from:  
    #0 0x7db40edd5c2b in malloc (/opt/compiler-explorer/gcc-15.2.0/lib64/libasan.so.8+0x121c2b)  
    #1 0x0000004056ed in operator new(unsigned long) /app/example.cpp:19  
    #2 0x00000040c062 in std::basic_ostream<char, std::char_traits<char> >& boost::posix_time::operator<< <char, std::char_traits<char> >(std::basic_ostream<char, std::char_traits<char> >&, boost::posix_time::time_duration const&) /app/boost/include/boost/date_time/posix_time/posix_time_io.hpp:190  
    #3 0x000000405ab9 in main /app/example.cpp:50  
    #4 0x7db40e029d8f  (/lib/x86_64-linux-gnu/libc.so.6+0x29d8f)  
  
... (additional indirect leaks reported) ...  
  
SUMMARY: AddressSanitizer: 831 byte(s) leaked in 4 allocation(s).  
```  
  
**Proposed fix:**  
  
The issue is in `include/boost/date_time/posix_time/posix_time_io.hpp`. The dynamically allocated facet objects are not deleted if an exception occurs during locale creation. The fix is to wrap the allocations in try-catch blocks and ensure proper cleanup.  
  
```diff  
diff --git a/include/boost/date_time/posix_time/posix_time_io.hpp b/include/boost/date_time/posix_time/posix_time_io.hpp  
index 6a72e32..7da07c3 100644  
--- a/include/boost/date_time/posix_time/posix_time_io.hpp  
+++ b/include/boost/date_time/posix_time/posix_time_io.hpp  
@@ -187,10 +187,20 @@ namespace posix_time {  
       //since we would always need to reconstruct for every time period  
       //if the locale did not already exist.  Of course this will be overridden  
       //if the user imbues as some later point.  
-      custom_ptime_facet* f = new custom_ptime_facet();  
-      std::locale l = std::locale(os.getloc(), f);  
-      os.imbue(l);  
-      f->put(oitr, os, os.fill(), td);  
+      custom_ptime_facet* f = nullptr;  
+      try {  
+        custom_ptime_facet* f = new custom_ptime_facet();  
+        f_ = f;  
+        std::locale l = std::locale(os.getloc(), f);  
+        f_ = nullptr;  
+        os.imbue(l);  
+        f->put(oitr, os, os.fill(), td);  
+      } catch(...) {  
+        if(f_) {  
+          delete f_;  
+        }  
+        throw;  
+      }  
     }  
     return os;  
   }  
@@ -211,10 +221,20 @@ namespace posix_time {  
           std::use_facet<time_input_facet_local>(is.getloc()).get(sit, str_end, is, td);  
         }  
         else {  
-          time_input_facet_local* f = new time_input_facet_local();  
-          std::locale l = std::locale(is.getloc(), f);  
-          is.imbue(l);  
-          f->get(sit, str_end, is, td);  
+          time_input_facet_local* f_ = nullptr;  
+          try {  
+            time_input_facet_local* f = new time_input_facet_local();  
+            f_ = f;  
+            std::locale l = std::locale(is.getloc(), f);  
+            f_ = nullptr;  
+            is.imbue(l);  
+            f->get(sit, str_end, is, td);  
+          } catch(...) {  
+            if(f_) {  
+              delete f_;  
+            }  
+            throw;  
+          }  
         }  
       }  
       catch(...) {  
```  
  
This change ensures that if an exception is thrown during the creation of the locale, the dynamically allocated facet is properly deleted before rethrowing the exception.  
  
[posix_time_io.patch](https://github.com/user-attachments/files/24236437/posix_time_io.patch)
