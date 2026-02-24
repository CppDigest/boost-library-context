# #334 Cleanup hash_combine usage. [Merged]

> Username: jzmaddock  
> Created at: 2021-06-06 09:08:16 UTC  
> Updated at: 2021-06-07 16:15:58 UTC  
> Merged at: 2021-06-07 16:15:58 UTC  
> Closed at: 2021-06-07 16:15:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/334  



---

## Review 1 [Commented]

> Username: ckormanyos  
> Created_at: 2021-06-06 09:28:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/334#pullrequestreview-676845699  

📁 include/boost/multiprecision/detail/hash.hpp

```diff
  26 |     // gmp types require explicit casting
  20 |-     seed = static_cast<std::size_t>(static_cast<T>(seed) ^ (v + 0x9e3779b9 + (seed<<6) + (seed>>2)));
  27 |+     seed = seed ^ (hash_value(v) + 0x9e3779b9 + (seed<<6) + (seed>>2));
```

> Username: ckormanyos  
> Created_at: 2021-06-06 09:28:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/334#discussion_r646103608  

Hi John. I have experienced difficulties with this line, but never discussed them. For me it is difficult to intuitively understand the operator precedence and the types of the integers. Are they meant to be int32_t or int? These are different things. And sure there are clear precedence rules, but xor, add, shift, it's confusing. I tried to add parentheses to this line in offline experiments once and completely failed. Would it be possible to clarify operator precedence, integral types with type information and parantheses?

> Username: jzmaddock  
> Created_at: 2021-06-06 12:20:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/334#discussion_r646124916  

Nod.  The original code from Boost.Hash uses std::size_t throughout.  When I changed things to make the function vararg I broke that, which resulted in a slew of compiler warnings :(  
  
This patch makes everything size_t's again since the `0x9e3779b9` is implicitly unsigned.  There shouldn't be any operator precedence issues now since `(seed<<6)` is still a size_t.  So we have the addition of 3 size_t's and an unsigned in `(hash_value(v) + 0x9e3779b9 + (seed<<6) + (seed>>2))` which is then xor'ed with `seed`.

> Username: jzmaddock  
> Created_at: 2021-06-06 12:21:17 UTC  
> Updated_at: 2021-06-06 12:21:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/334#discussion_r646124988  

But I see I've broken __int128 support since std::hash doesn't support that.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-06-07 16:15:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/334#issuecomment-856075069  

Failures are unrelated to this PR, so merging.

---
