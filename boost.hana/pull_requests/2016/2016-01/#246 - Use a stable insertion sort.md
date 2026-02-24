# #246 [sort] Use a stable insertion sort [Merged]

> Username: ldionne  
> Created at: 2016-01-28 02:55:53 UTC  
> Updated at: 2016-01-28 17:08:18 UTC  
> Merged at: 2016-01-28 17:07:47 UTC  
> Closed at: 2016-01-28 17:07:47 UTC  
> Url: https://github.com/boostorg/hana/pull/246  

This is related to #205. Preliminary benchmarking seems to indicate that this insertion sort technique is **way** better than the eager sorting done in #205.

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-01-28 16:31:49 UTC  
> Url: https://github.com/boostorg/hana/pull/246#issuecomment-176266510  

Here is a benchmark of the different sorting techniques. The _original quicksort_ represents the current sort, the _eager sort_ represents #205, and the two _insertion sorts_ are this PR.   
  
![screen shot 2016-01-28 at 11 30 48](https://cloud.githubusercontent.com/assets/700834/12650912/a90e2fb4-c5b2-11e5-88ef-f34c2a883e8e.png)

---
