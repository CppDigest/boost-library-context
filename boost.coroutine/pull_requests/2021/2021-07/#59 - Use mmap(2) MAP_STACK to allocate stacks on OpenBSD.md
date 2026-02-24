# #59 Use mmap(2) MAP_STACK to allocate stacks on OpenBSD [Merged]

> Username: brad0  
> Created at: 2021-07-10 06:27:18 UTC  
> Updated at: 2021-08-14 15:44:21 UTC  
> Merged at: 2021-08-14 15:44:17 UTC  
> Closed at: 2021-08-14 15:44:17 UTC  
> Url: https://github.com/boostorg/coroutine/pull/59  

Since OpenBSD 6.4 (https://www.openbsd.org/64.html), the stack pointer  
must point to MAP_STACK memory, or the kernel may kill the process  
with a signal.  All stack allocators must pass MAP_STACK to mmap(2).  
  
I took inspiration from boostorg/context@7e14ab9 for this diff.

---

## Comment 1

> Username: brad0  
> Created_at: 2021-08-13 19:28:35 UTC  
> Url: https://github.com/boostorg/coroutine/pull/59#issuecomment-898675655  

1.77 is out. This is one of the very few patches we have in our tree that I'd like to get in.

---

## Comment 2

> Username: olk  
> Created_at: 2021-08-14 15:44:21 UTC  
> Url: https://github.com/boostorg/coroutine/pull/59#issuecomment-898909976  

ty

---
