# #208 Add include to extend.hpp for memory. [Merged]

> Username: 0x55555555  
> Created at: 2021-04-14 12:54:02 UTC  
> Updated at: 2021-10-09 08:23:39 UTC  
> Merged at: 2021-10-09 08:23:39 UTC  
> Closed at: 2021-10-09 08:23:39 UTC  
> Url: https://github.com/boostorg/process/pull/208  

`extend.hpp` uses `std::shared_ptr` as a member of `posix_executor` but does not include it.

---
