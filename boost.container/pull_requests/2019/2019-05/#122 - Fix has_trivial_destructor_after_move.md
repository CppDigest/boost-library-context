# #122 Fix has_trivial_destructor_after_move [Merged]

> Username: kariya-mitsuru  
> Created at: 2019-05-30 03:27:26 UTC  
> Updated at: 2019-06-08 13:31:07 UTC  
> Merged at: 2019-06-08 10:48:27 UTC  
> Closed at: 2019-06-08 10:48:27 UTC  
> Url: https://github.com/boostorg/container/pull/122  

Most template type parameters 'Allocator' were modified their default type to void since 1.70.0.  
These modifications cause has_trivial_destructor_after_move to compile error or yield wrong result.  
So, fix them by changing specializations of has_trivial_destructor_after_move.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-06-08 10:48:40 UTC  
> Url: https://github.com/boostorg/container/pull/122#issuecomment-500114732  

Many thanks for the patch!

---
