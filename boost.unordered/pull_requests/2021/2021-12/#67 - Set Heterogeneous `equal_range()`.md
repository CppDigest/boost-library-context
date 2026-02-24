# #67 Set Heterogeneous `equal_range()` [Merged]

> Username: cmazakas  
> Created at: 2021-12-27 21:00:47 UTC  
> Updated at: 2021-12-29 15:10:32 UTC  
> Merged at: 2021-12-28 23:42:40 UTC  
> Closed at: 2021-12-28 23:42:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/67  



---

## Comment 1

> Username: pdimov  
> Created_at: 2021-12-28 23:43:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/67#issuecomment-1002324307  

Looks good. We should however test `equal_range(1)` as well, so that the multiset case tests both a range of size 1 and a range of size >1.

---
