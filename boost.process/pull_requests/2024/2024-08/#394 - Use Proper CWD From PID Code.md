# #394 [DragonFly BSD] Use Proper CWD From PID Code [Closed]

> Username: ghost  
> Created at: 2024-08-05 23:56:20 UTC  
> Updated at: 2024-09-30 03:26:34 UTC  
> Closed at: 2024-09-30 03:26:33 UTC  
> Url: https://github.com/boostorg/process/pull/394  

Using an additional `sysctl()` call to predetermine the length of the buffer always returns a length of zero due to an OS-level bug, so we are forced to use a fixed-length buffer instead, which doesn't dynamically resize to the necessary size to fit buffer contents.

---
