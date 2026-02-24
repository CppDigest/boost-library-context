# #256 - Missing template argument list after template keyword in interprocess/segment_manager.hpp [Closed]

> Username: mtmorgan  
> Created at: 2025-03-30 14:16:37 UTC  
> Updated at: 2025-04-14 20:07:29 UTC  
> Closed at: 2025-04-14 20:07:27 UTC  
> Url: https://github.com/boostorg/interprocess/issues/256  

Trying to compile  
```c++  
#include <boost/interprocess/segment_manager.hpp>  
  
int main(const int argc, const char **argv) { return 0; }  
```  
with  
```sh  
% clang --version  
Apple clang version 17.0.0 (clang-1700.0.13.3)  
Target: arm64-apple-darwin24.3.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```  
(CommandLineTools 16.3RC; release imminent) results in  
```sh  
% clang -I"./" -Wall clang-bug.cpp  
In file included from clang-bug.cpp:1:  
./boost/interprocess/segment_manager.hpp:1055:41: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1055 |          hdr = block_header_t::template from_first_header(reinterpret_cast<index_data_t*>((void*)((char*)buffer_ptr + front_space)));  
      |                                         ^  
1 error generated.  
```  
The fix appears to be  
```diff  
diff --git a/include/boost/interprocess/segment_manager.hpp b/include/boost/interprocess/segment_manager.hpp  
index 024e2b4..192dfef 100644  
--- a/include/boost/interprocess/segment_manager.hpp  
+++ b/include/boost/interprocess/segment_manager.hpp  
@@ -1052,7 +1052,7 @@ class segment_manager  
             return ipcdetail::null_or_bad_alloc<object_type>(dothrow);  
   
          front_space = block_header_t::template front_space_with_header<t_alignment, index_data_t>();  
-         hdr = block_header_t::template from_first_header(reinterpret_cast<index_data_t*>((void*)((char*)buffer_ptr + front_space)));  
+         hdr = block_header_t::from_first_header(reinterpret_cast<index_data_t*>((void*)((char*)buffer_ptr + front_space)));  
       }  
       else{  
          const size_type total_size = block_info.template total_named_size<t_alignment, CharT>(namelen);  
```  
Perhaps there are other similar occurrences in the code base?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-04-14 20:07:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/256#issuecomment-2802865898  

Thanks for the report! Fixed in develop.
