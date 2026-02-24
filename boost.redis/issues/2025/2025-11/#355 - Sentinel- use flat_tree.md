# #355 - Sentinel: use flat_tree [Closed]

> Username: anarthal  
> Created at: 2025-11-19 17:12:53 UTC  
> Updated at: 2026-01-28 15:30:21 UTC  
> Closed at: 2026-01-28 15:30:21 UTC  
> Url: https://github.com/boostorg/redis/issues/355  

It's more efficient, and it allows implementing parsing better, since the tree contains the number of messages. [This comment](https://github.com/boostorg/redis/pull/345#discussion_r2530369547) should be addressed in an easier way then.

---

## Comment 1

> Username: anarthal  
> Created at: 2026-01-28 15:30:21 UTC  
> Url: https://github.com/boostorg/redis/issues/355#issuecomment-3811974276  

Completed as per #391
