# #9 - Build fails with GCC 14.1 [Closed]

> Username: gaspacio  
> Created at: 2024-05-31 14:37:07 UTC  
> Updated at: 2024-06-03 08:52:04 UTC  
> Closed at: 2024-06-03 08:52:03 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/9  

Bok dečki,  
I've been using the library with GCC 13.2 but after updating to 14.1 this error appears:  
  
```  
/root/prj/x86_build/_deps/async-mqtt5-src/include/async_mqtt5/detail/control_packet.hpp: In member function 'void async_mqtt5::packet_id_allocator::free(uint16_t)':  
/root/prj/x86_build/_deps/async-mqtt5-src/include/async_mqtt5/detail/control_packet.hpp:143:32: error: 'upper_bound' is not a member of 'std'; did you mean 'lower_bound'?  
  
  143 |                 auto it = std::upper_bound(  
      |                                ^~~~~~~~~~~  
      |                                lower_bound  
```  
Adding `#include <algorithm>` (which defines std::upper_bound) in `include/async_mqtt5/detail/control_packet.hpp` fixed the build for me.

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-06-03 08:52:03 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/9#issuecomment-2144645468  

Hello!  
  
Thank you for reporting this issue!  
I pushed the commit to fix the compilation error you described.
