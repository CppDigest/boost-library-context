# #236 Avoid including deprecated atomic_count.hpp header [Merged]

> Username: Lastique  
> Created at: 2025-02-28 20:24:02 UTC  
> Updated at: 2025-04-24 21:59:28 UTC  
> Merged at: 2025-03-01 14:38:33 UTC  
> Closed at: 2025-03-01 14:38:33 UTC  
> Url: https://github.com/boostorg/wave/pull/236  

`boost/detail/atomic_count.hpp` was moved to `boost/smart_ptr/detail` and the old header is deprecated and emits warnings.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2025-03-01 14:38:40 UTC  
> Url: https://github.com/boostorg/wave/pull/236#issuecomment-2692262977  

Thanks!

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-04-24 21:59:27 UTC  
> Url: https://github.com/boostorg/wave/pull/236#issuecomment-2828958578  

Looks like this didn't make it into the 1.88.0 release. Please, merge to master.

---
