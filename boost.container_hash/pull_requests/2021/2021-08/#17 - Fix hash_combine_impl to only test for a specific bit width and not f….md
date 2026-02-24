# #17 Fix hash_combine_impl to only test for a specific bit width and not f… [Merged]

> Username: pdimov  
> Created at: 2021-08-10 18:22:39 UTC  
> Updated at: 2021-10-12 20:37:48 UTC  
> Merged at: 2021-10-12 20:37:48 UTC  
> Closed at: 2021-10-12 20:37:48 UTC  
> Url: https://github.com/boostorg/container_hash/pull/17  

…or concrete types  
  
Fixes #8.

---

## Review 1 [Commented]

> Username: e4lam  
> Created_at: 2021-08-18 12:54:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/container_hash/pull/17#pullrequestreview-732823148  

This change didn't make it into boost 1.77 right?

📁 include/boost/container_hash/hash.hpp

```diff
 342 |+             inline static boost::uint64_t fn(boost::uint64_t h, boost::uint64_t k)
 343 |+             {
 344 |+                 const boost::uint64_t m = (boost::uint64_t(0xc6a4a793) << 32) + 0x5bd1e995;
```

> Username: e4lam  
> Created_at: 2021-08-18 12:53:08 UTC  
> Updated_at: 2021-08-18 12:54:06 UTC  
> Url: https://github.com/boostorg/container_hash/pull/17#discussion_r691207460  

Just curious, why the switch away from `UINT64_C()` here? When I read bit manipulation code I think I still expect to see `|` used rather than `+`.


---

## Comment 2

> Username: pdimov  
> Created_at: 2021-08-18 13:12:43 UTC  
> Url: https://github.com/boostorg/container_hash/pull/17#issuecomment-901104244  

No, it didn't make it into 1.77; it hasn't been merged at all.  
  
UINT64_C still warns in pedantic mode on GCC or Clang (or both; I forget which) because of the long long constant, and it's not possible to disable the warning, because it happens at preprocessing time. Splitting the constant is the only warning-free way I've found.

---
