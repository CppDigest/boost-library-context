# #46 - Warnings when compiling on 32 bits platform [Closed]

> Username: pendenaor  
> Created at: 2023-05-16 09:23:03 UTC  
> Updated at: 2023-05-29 15:53:34 UTC  
> Closed at: 2023-05-29 15:53:34 UTC  
> Url: https://github.com/boostorg/static_string/issues/46  

Having some warnings when compiling on 32 bits arm with GCC 7.3  
```  
static_string.hpp:6366:54: warning: left shift count >= width of type [-Wshift-count-overflow]  
     std::size_t const m = (std::size_t(0xe9846af) << 32) + 0x9b1a615d;  
                                                      ^~  
static_string.hpp:6367:21: warning: right shift count >= width of type [-Wshift-count-overflow]  
     seed ^= seed >> 32;  
                     ^~  
static_string.hpp:6369:21: warning: right shift count >= width of type [-Wshift-count-overflow]  
     seed ^= seed >> 32;  
                     ^~  
```  
Apparently, this code calculate a hash on 64 bits but `std::size_t` is only 32 bits on my platform.  
A further exploration indicate that this code won't be used in my case but is still be compiled.   
So, just noisy misleading warnings.
