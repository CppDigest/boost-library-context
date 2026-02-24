# #302 fix: severe warning of aggressive loop optimizations [Closed]

> Username: spykyvenator  
> Created at: 2025-06-12 09:15:32 UTC  
> Updated at: 2025-06-12 10:04:01 UTC  
> Closed at: 2025-06-12 10:04:01 UTC  
> Url: https://github.com/boostorg/container/pull/302  

This small patch fixes the severe warning:  
/var/tmp/portage/dev-libs/boost-1.88.0-r1/work/boost_1_88_0-abi_x86_64.amd64/libs/container/src/dlmalloc_ext_2_8_6.c:1085:23: warning: iteration 2305843009213693951 invokes undefined behavior [-Waggressive-loop-optimizations]  
I got when compiling with gcc-15

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2025-06-12 09:58:49 UTC  
> Url: https://github.com/boostorg/container/pull/302#issuecomment-2965959225  

Tha patch is not correct, as ssize_t is not standard C or C++. It might work on POSIX systems, but not in other operating system like windows.

---

## Comment 2

> Username: spykyvenator  
> Created_at: 2025-06-12 10:03:51 UTC  
> Url: https://github.com/boostorg/container/pull/302#issuecomment-2965979222  

Ok, my bad

---
