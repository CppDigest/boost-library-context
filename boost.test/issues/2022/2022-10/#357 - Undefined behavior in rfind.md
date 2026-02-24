# #357 - Undefined behavior in rfind [Closed]

> Username: Flamefire  
> Created at: 2022-10-02 20:46:08 UTC  
> Updated at: 2023-02-06 17:20:24 UTC  
> Closed at: 2023-02-06 17:20:24 UTC  
> Url: https://github.com/boostorg/test/issues/357  

https://github.com/boostorg/test/blob/develop/include/boost/test/utils/basic_cstring/basic_cstring.hpp#L576  
  
This takes the address to one element before begin which is undefined behavior and recent compilers got smart enough to notice this with -Warray-bounds
