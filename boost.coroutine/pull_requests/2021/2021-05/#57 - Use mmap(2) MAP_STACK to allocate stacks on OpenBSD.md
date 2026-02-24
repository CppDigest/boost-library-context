# #57 Use mmap(2) MAP_STACK to allocate stacks on OpenBSD [Closed]

> Username: brad0  
> Created at: 2021-05-07 03:21:21 UTC  
> Updated at: 2021-07-10 06:18:19 UTC  
> Closed at: 2021-07-10 06:18:19 UTC  
> Url: https://github.com/boostorg/coroutine/pull/57  

Since OpenBSD 6.4 (https://www.openbsd.org/64.html), the stack pointer  
must point to MAP_STACK memory, or the kernel may kill the process  
with a signal.  All stack allocators must pass MAP_STACK to mmap(2).  
  
I took inspiration from https://github.com/boostorg/context/commit/7e14ab9e456a365b69081b573746402fffc901fc  
for this diff.

---

## Comment 1

> Username: brad0  
> Created_at: 2021-06-29 17:37:42 UTC  
> Url: https://github.com/boostorg/coroutine/pull/57#issuecomment-870788387  

ping.

---
