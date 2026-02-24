# #1044 - boost_1_88_0 addr_base_msvc.hpp compile fail [Closed]

> Username: oniisancr  
> Created at: 2025-06-01 06:24:31 UTC  
> Updated at: 2025-06-02 08:17:03 UTC  
> Closed at: 2025-06-02 08:17:03 UTC  
> Url: https://github.com/boostorg/boost/issues/1044  

gcc.compile.c++ bin.v2\libs\stacktrace\build\gcc-15\debug\x86_64\link-static\threadapi-win32\threading-multi\visibility-hidden\windbg.o  
In file included from ./boost/stacktrace/detail/frame_msvc.ipp:23,  
from C:/code/boost_1_88_0/libs/stacktrace/src/windbg.cpp:13:  
./boost/stacktrace/detail/addr_base_msvc.hpp:31:15: error: 'uintptr_t' in namespace 'std' does not name a type  
31 | inline std::uintptr_t get_own_proc_addr_base(const void* addr) {  
  
  
![Image](https://github.com/user-attachments/assets/a5c07caa-b3b8-4385-97d0-f74cf1c4e406)  
  
  
## need inclde header in addr_base_msvc.hpp  
  
`#include <cstdint>`  
  
  
![Image](https://github.com/user-attachments/assets/f49a9b48-bdb6-4b26-b138-bd007a7071f7)
