# #269 - `__NR_map_shadow_stack` redefinition [Closed]

> Username: Lastique  
> Created at: 2024-09-12 16:33:11 UTC  
> Updated at: 2025-01-06 17:52:24 UTC  
> Closed at: 2024-12-26 07:09:08 UTC  
> Url: https://github.com/boostorg/context/issues/269  

When compiling Boost.Fiber on Kubuntu 24.04 (kernel version 6.8.0-44, glibc 2.39-0ubuntu8.3) I get these warnings:  
  
```  
In file included from ./boost/context/fiber.hpp:12,  
                 from ./boost/fiber/context.hpp:28,  
                 from libs/fiber/src/algo/algorithm.cpp:9:  
./boost/context/fiber_fcontext.hpp:52: warning: "__NR_map_shadow_stack" redefined  
   52 | #  define __NR_map_shadow_stack 451  
      |   
In file included from /usr/include/x86_64-linux-gnu/asm/unistd.h:20,  
                 from /usr/include/x86_64-linux-gnu/sys/syscall.h:24,  
                 from /usr/include/syscall.h:1,  
                 from /usr/include/c++/13/bits/atomic_wait.h:46,  
                 from /usr/include/c++/13/bits/atomic_base.h:42,  
                 from /usr/include/c++/13/atomic:41,  
                 from ./boost/fiber/algo/algorithm.hpp:9,  
                 from libs/fiber/src/algo/algorithm.cpp:7:  
/usr/include/x86_64-linux-gnu/asm/unistd_64.h:368: note: this is the location of the previous definition  
  368 | #define __NR_map_shadow_stack 453  
      |   
```  
  
I think, the code should check if `__NR_map_shadow_stack` is defined and use the existing define, if possible.  
  
I should say that `__NR_map_shadow_stack` is defined to 453 in both `unistd_64.h` and `unistd_32.h` on my machine. I'm not sure if the value 451 is correct to begin with.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-01-06 17:52:22 UTC  
> Url: https://github.com/boostorg/context/issues/269#issuecomment-2573615869  

Could I request this to get merged to master, please?
