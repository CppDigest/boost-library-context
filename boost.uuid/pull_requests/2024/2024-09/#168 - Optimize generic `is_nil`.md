# #168 Optimize generic `is_nil` [Closed]

> Username: Lastique  
> Created at: 2024-09-16 13:14:41 UTC  
> Updated at: 2026-01-03 01:18:58 UTC  
> Closed at: 2024-09-22 16:48:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/168  

Reduce the number of generated branches in the generic `is_nil` by using bitwise operations.  
  
Note that a similar optimization is possible for `operator==`, but in that case there may be a slight reduction in performance if UUIDs are mostly unequal, and a slight speedup if they are mostly equal. If you're interested, I can add the change to `operator==` as well.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-09-16 14:50:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/168#issuecomment-2353147590  

Compilers already do this transformation by the way.

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-09-16 17:14:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/168#issuecomment-2353478820  

Ok, you can close it then.  
  
I just prefer to not rely on compilers, when possible. :)

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-09-16 17:28:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/168#issuecomment-2353505451  

I'll wait for CE to have 1.86 so that I can look at the codegen (again.)

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-09-22 15:04:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/168#issuecomment-2366828734  

Now that 1.86 is on CE (somewhat, that is, not on all compilers), I could verify that this optimization is done by approximately all GCC and Clang versions we support. E.g. https://godbolt.org/z/zPoEP6xoj.

---

## Comment 5

> Username: Lastique  
> Created_at: 2024-09-22 16:48:36 UTC  
> Updated_at: 2024-09-22 16:48:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/168#issuecomment-2366868172  

Ok, thanks.

---
