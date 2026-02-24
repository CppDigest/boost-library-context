# #105 Follow-up for #104 [Closed]

> Username: georgthegreat  
> Created at: 2026-01-15 17:40:52 UTC  
> Updated at: 2026-01-16 18:22:44 UTC  
> Closed at: 2026-01-16 11:46:03 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/105  

Missing this one in the original PR

---

## Comment 1

> Username: gennaroprota  
> Created_at: 2026-01-16 10:21:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/105#issuecomment-3759280385  

Fixed by amending the original commit. Thanks. (Do you really have a standard library implementation which uses raw pointers as iterators?)

---

## Comment 2

> Username: georgthegreat  
> Created_at: 2026-01-16 11:45:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/105#issuecomment-3759674435  

Unfortunately, we do.  
And out codebase is so huge that we do not know how to perform a migration.

---
