# #359 v2: sync initial value for exit code with v1 [Merged]

> Username: ceggers-arri  
> Created at: 2024-03-27 12:14:22 UTC  
> Updated at: 2024-03-31 22:40:48 UTC  
> Merged at: 2024-03-31 22:40:48 UTC  
> Closed at: 2024-03-31 22:40:48 UTC  
> Url: https://github.com/boostorg/process/pull/359  

The initial value for the exit_code (0x7f) in v2 doesn't work with musl libc. Here WIFSIGNALED(0x7f) expands to a non-zero value:  
  
http://git.musl-libc.org/cgit/musl/tree/include/sys/wait.h#n54

---
