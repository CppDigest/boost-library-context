# #1042 [index] Fix and improve rtree experimental features [Merged]

> Username: awulkiew  
> Created at: 2022-07-19 20:53:23 UTC  
> Updated at: 2022-07-20 14:51:06 UTC  
> Merged at: 2022-07-20 14:51:06 UTC  
> Closed at: 2022-07-20 14:51:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1042  

Fix access specifier preventing use of experimental iterators.  
Allow more granular control over enabled experimental features.

---

## Review 1 [Approved]

> Username: jwillikers  
> Created_at: 2022-07-19 22:19:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1042#pullrequestreview-1044142632  

Works great for me! By defining `BOOST_GEOMETRY_INDEX_DETAIL_EXPERIMENTAL_ITERATORS`, I also don't have to include Boost Serialization.

---
