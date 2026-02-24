# #161 Complete deduction tests, implement guides for all containers [Merged]

> Username: cmazakas  
> Created at: 2022-11-07 23:57:18 UTC  
> Updated at: 2022-11-08 15:27:06 UTC  
> Merged at: 2022-11-08 13:27:27 UTC  
> Closed at: 2022-11-08 13:27:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/161  

Closes https://github.com/boostorg/unordered/issues/149

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2022-11-08 01:46:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/161#pullrequestreview-1171326409  

📁 include/boost/unordered/unordered_flat_map.hpp

```diff
 640 |+       template <typename T>
 641 |+       using iter_to_alloc_t =
 642 |+         typename std::pair<iter_key_t<T> const, iter_val_t<T> >;
```

> Username: pdimov  
> Created_at: 2022-11-08 01:46:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/161#discussion_r1016064944  

I don't think `typename` is needed here.


---

## Comment 2

> Username: joaquintides  
> Created_at: 2022-11-08 09:33:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/161#issuecomment-1306906387  

Why's the `boost::type_identity` trick only used for maps and not for sets?

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-11-08 13:13:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/161#issuecomment-1307199457  

This passed CI; should I merge it to develop and master? We can fix the remaining things later.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2022-11-08 13:24:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/161#issuecomment-1307218189  

I'm OK with that. Docs should be updated --I can do that, but, again, not this week

---
