# #68 Fix futex support on OpenBSD. [Closed]

> Username: brad0  
> Created at: 2024-05-18 07:58:09 UTC  
> Updated at: 2024-05-19 07:22:47 UTC  
> Closed at: 2024-05-19 07:21:06 UTC  
> Url: https://github.com/boostorg/atomic/pull/68  

No indirect syscalls are allowed. The futex() function which issues  
a direct syscall is required.  
  
https://man.openbsd.org/futex

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-05-18 09:23:01 UTC  
> Url: https://github.com/boostorg/atomic/pull/68#issuecomment-2118733448  

On May 18, 2024 10:58:31 AM Brad Smith ***@***.***> wrote:  
  
> No indirect syscalls are allowed. The futex() function which issues  
> a direct syscall is required.  
  
Could you provide more details? What do you mean by "not allowed"? Does   
invoking the syscall directly not work? What is the error?  
  
Also, is the futex wrapper function universally available on OpenBSD?

---

## Comment 2

> Username: brad0  
> Created_at: 2024-05-18 09:34:52 UTC  
> Url: https://github.com/boostorg/atomic/pull/68#issuecomment-2118737077  

> On May 18, 2024 10:58:31 AM Brad Smith ***@***.***> wrote: No indirect syscalls are allowed. The futex() function which issues a direct syscall is required.  
> Could you provide more details? What do you mean by "not allowed"? Does invoking the syscall directly not work? What is the error? Also, is the futex wrapper function universally available on OpenBSD?  
  
As in support for syscall(2) has been removed.  
  
https://github.com/openbsd/src/commit/cafeb892b121ee89c39c2b940e8ccd6950f50009  
  
Yes, it is.

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-05-18 23:08:34 UTC  
> Updated_at: 2024-05-18 23:09:30 UTC  
> Url: https://github.com/boostorg/atomic/pull/68#issuecomment-2119025437  

> > Also, is the futex wrapper function universally available on OpenBSD?  
>   
> Yes, it is.  
  
No, it's not. Apparently, `futex` only appeared in [OpenBSD 6.2](https://man.openbsd.org/OpenBSD-6.2/futex.2#HISTORY). The current code tests that by means of testing whether the syscall number is defined.

---

## Comment 4

> Username: Lastique  
> Created_at: 2024-05-19 00:31:29 UTC  
> Url: https://github.com/boostorg/atomic/pull/68#issuecomment-2119042562  

Can you test if 6ee3138 works on OpenBSD?

---

## Comment 5

> Username: brad0  
> Created_at: 2024-05-19 02:13:38 UTC  
> Url: https://github.com/boostorg/atomic/pull/68#issuecomment-2119065390  

> Can you test if [6ee3138](https://github.com/boostorg/atomic/commit/6ee31382b271193064db2bfe0a446260c5b344c0) works on OpenBSD?  
  
That looks fine and builds on -current.

---
