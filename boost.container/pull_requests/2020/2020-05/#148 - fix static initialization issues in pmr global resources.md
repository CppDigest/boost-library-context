# #148 fix static initialization issues in pmr global resources [Closed]

> Username: mucki-at  
> Created at: 2020-05-20 00:20:55 UTC  
> Updated at: 2020-05-23 22:38:59 UTC  
> Closed at: 2020-05-23 21:40:25 UTC  
> Url: https://github.com/boostorg/container/pull/148  

make sure that pmr global resources are constructed on first use so that they can be successfully used during static initialization (ie: before main()). Caveats: unless "magic statics" are supported by the compiler the very first call to new_delete_resource() and null_memory_resource() is not thread-safe.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2020-05-23 21:40:17 UTC  
> Url: https://github.com/boostorg/container/pull/148#issuecomment-633143971  

Thanks for the report. Based on your patch, I've implemented a solution that also works in C++03 and ensures static initialization based on existing boost singleton implementations.

---

## Comment 2

> Username: mucki-at  
> Created_at: 2020-05-23 21:56:39 UTC  
> Url: https://github.com/boostorg/container/pull/148#issuecomment-633145691  

Thank you, much appreciated. Unfortunately it doesn't quite fix the issue  
(see my comments in your commit)  
  
On Sat, May 23, 2020 at 2:40 PM Ion Gaztañaga <notifications@github.com>  
wrote:  
  
> Closed #148 <https://github.com/boostorg/container/pull/148> via 093467e  
> <https://github.com/boostorg/container/commit/093467e1d3e05b93b48e38517750239c6649a63e>  
> .  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/container/pull/148#event-3367446266>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AFR6C7IBVYCZJWJRE6L5ZDDRTA7FJANCNFSM4NFO2FLA>  
> .  
>

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2020-05-23 22:38:59 UTC  
> Url: https://github.com/boostorg/container/pull/148#issuecomment-633149551  

Let's see if this fixes the issue and also supports C++03

---
