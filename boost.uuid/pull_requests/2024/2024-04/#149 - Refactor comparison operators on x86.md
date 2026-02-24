# #149 Refactor comparison operators on x86 [Merged]

> Username: Lastique  
> Created at: 2024-04-23 15:22:35 UTC  
> Updated at: 2024-04-23 23:36:58 UTC  
> Merged at: 2024-04-23 23:12:44 UTC  
> Closed at: 2024-04-23 23:12:44 UTC  
> Url: https://github.com/boostorg/uuid/pull/149  

The first commit refactors `operator<` and `operator<=>` on x86 to use the shared implementation.  
  
The second commit restores the previous behavior, when SSE2 implementation was used on x86, even when `uint128_t` is available. This was changed in https://github.com/boostorg/uuid/commit/b3e3a59a9869eb70fb089dbb88b4e29fe7c91c5e.

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-04-23 15:24:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/149#issuecomment-2072690820  

Please, also note that since you switched to `<cstdint>` all `(u)intN_t` need to be qualified with `std::`. This commit takes care of that for `uuid_x86.ipp`.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-23 17:03:18 UTC  
> Url: https://github.com/boostorg/uuid/pull/149#issuecomment-2072941472  

Please don't put unrelated changes into the same PR.

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-04-23 17:22:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/149#issuecomment-2072979382  

I'll move the second commit to a separate PR.

---
