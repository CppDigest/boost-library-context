# #58 Update calc_num_blocks(). Avoid using modulo operator. [Closed]

> Username: einiemand  
> Created at: 2021-01-13 00:23:57 UTC  
> Updated at: 2024-11-26 20:55:55 UTC  
> Closed at: 2024-11-26 20:55:55 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/58  

`num_bits + bits_per_block - 1` is unlikely to overflow. After the change, it could be a little faster.

---

## Review 1 [Commented]

> Username: jeking3  
> Created_at: 2022-04-27 13:21:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/58#pullrequestreview-954895470  

What are the boundary cases that are unlikely, and are there tests to verify this change is safe?  If num_bits approaches size_type (which is a std::size_t, which usually depends on the address-model *but is implementation-specific*), this could overflow.  
  
Given this is a one-time cost at setup of the dynamic_bitset, is breaking the unlikely case worth it?

---

## Comment 2

> Username: akr-akari  
> Created_at: 2022-04-29 07:21:38 UTC  
> Updated_at: 2022-05-03 03:19:44 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/58#issuecomment-1112947921  

`return static_cast<size_type>(std::ceil(static_cast<double>(num_bits) / bits_per_block));`

---

## Comment 3

> Username: jeking3  
> Created_at: 2024-11-26 20:55:55 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/58#issuecomment-2501918411  

While unlikely to overflow, it is still possible.  If the overflow situation can be tested for and faster logic used, that seems fine.  Otherwise the proposed change is not safe in all situations, despite being an extreme outlier, as the original code is.  Given this, I am closing this PR since it has not had an update for years.

---
