# #383 - x86 inline assembly should provide an alternative for Intel syntax [Open]

> Username: lhmouse  
> Created at: 2023-03-24 11:48:55 UTC  
> Updated at: 2023-03-24 11:48:55 UTC  
> Url: https://github.com/boostorg/thread/issues/383  

Instead of  
https://github.com/boostorg/thread/blob/52b62ee7b029028126c4eaf36e377833b0666510/include/boost/thread/win32/interlocked_read.hpp#L158  
  
we should have  
```c  
 __asm__ __volatile__ ("{ movl %1, %0 | mov %0, %1 }" : "=r" (res) : "m" (*x) : "memory");   
```
