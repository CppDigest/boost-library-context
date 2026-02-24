# #378 posix: check glibc version for ::close_range function [Closed]

> Username: T3RR7  
> Created at: 2024-06-18 18:01:08 UTC  
> Updated at: 2024-12-20 01:47:46 UTC  
> Closed at: 2024-12-20 01:47:46 UTC  
> Url: https://github.com/boostorg/process/pull/378  

On old systems with old glibc, but new kernel (the common case is build in CI on ancient OS with old glibc for extended compatibility) there may not be `::close_range()` function. In this case build will fail.  
  
To avoid that we should properly check not only linux kernel version, but glibc version too.  
  
There is one another option to solve this issue is use raw system call with only checking kernel version, but it seems to me dirty solution.

---

## Comment 1

> Username: T3RR7  
> Created_at: 2024-06-18 18:05:08 UTC  
> Url: https://github.com/boostorg/process/pull/378#issuecomment-2176678587  

Moreover, I've just noticed that kernel version also checked wrong.   
According to:   
- https://man.archlinux.org/man/close_range.2.en#HISTORY  
- https://man7.org/linux/man-pages/man2/close_range.2.html  
  
`close_range()` available from kernel 5.9, so right check might look like:  
  
```cpp  
#if LINUX_VERSION_CODE >= KERNEL_VERSION(5,9,0)  
```

---

## Comment 2

> Username: T3RR7  
> Created_at: 2024-07-03 14:49:27 UTC  
> Updated_at: 2024-07-03 15:29:47 UTC  
> Url: https://github.com/boostorg/process/pull/378#issuecomment-2206387052  

@klemens-morgenstern  I've noticed that you pushed the fix to you devel branch, but you haven't provided option to fully disable close_range usage, as I understood.  
  
In case we build on new kernel, BUT with old glibc (for extended compatibility) the whole program will break, because of ENOTSUP error (a.k.a Unsupported system call).

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2024-10-24 23:39:47 UTC  
> Updated_at: 2024-10-24 23:42:53 UTC  
> Url: https://github.com/boostorg/process/pull/378#issuecomment-2436521784  

@T3RR7 does 41a4a5a  work?

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2024-12-20 01:47:46 UTC  
> Url: https://github.com/boostorg/process/pull/378#issuecomment-2556099458  

Assuming 41a4a5a did the trick

---
