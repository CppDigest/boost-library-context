# #1059 - object hash function should be order-independent [Closed]

> Username: vinniefalco  
> Created at: 2024-12-07 16:41:32 UTC  
> Updated at: 2024-12-14 19:04:20 UTC  
> Closed at: 2024-12-14 19:04:20 UTC  
> Url: https://github.com/boostorg/json/issues/1059  

I think the only way to correctly calculate the hash is to first sort the elements by key into a temporary array.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-12-14 11:07:43 UTC  
> Url: https://github.com/boostorg/json/issues/1059#issuecomment-2543059108  

Currently we defer to ContainerHash which does support hashing of unordered containers. It does so correctly (that is, equal containers produce the same hashes), but there's the question if the algorithms produces too many collisions. Are we concerned with that possibility? Performance-wise, we are unlikely to get anything better.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-12-14 19:04:04 UTC  
> Url: https://github.com/boostorg/json/issues/1059#issuecomment-2543313801  

I was wrong... you don't have to sort. You can calculate the digest of each element and add them up together, like Hash2 does in `hash_append_unordered_range`.
