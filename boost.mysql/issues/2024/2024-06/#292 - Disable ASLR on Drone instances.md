# #292 - Disable ASLR on Drone instances [Closed]

> Username: anarthal  
> Created at: 2024-06-26 10:50:51 UTC  
> Updated at: 2024-06-26 13:43:39 UTC  
> Closed at: 2024-06-26 13:43:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/292  

We're getting ASAN and TSAN failures apparently regarding an update to the Linux Kernel increasing the number of ASLR bits (https://stackoverflow.com/questions/77850769/fatal-threadsanitizer-unexpected-memory-mapping-when-running-on-linux-kernels). We should limit or disable ASLR to prevent failures.
