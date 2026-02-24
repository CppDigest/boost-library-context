# #406 [DragonFly BSD] Correct CWD from PID Code. [Merged]

> Username: ghost  
> Created at: 2024-09-30 03:36:07 UTC  
> Updated at: 2024-10-29 00:36:51 UTC  
> Merged at: 2024-10-24 23:01:56 UTC  
> Closed at: 2024-10-24 23:01:56 UTC  
> Url: https://github.com/boostorg/process/pull/406  

Using an additional `sysctl()` call to predetermine the length of the buffer always returns a length of zero due to an OS-level bug, so we are forced to use a fixed-length buffer instead, which doesn't dynamically resize to the necessary size to fit buffer contents.

---
