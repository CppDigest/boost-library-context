# #23 - Allow inserts with more than one hint [Open]

> Username: gbizzotto  
> Created at: 2019-04-26 10:49:31 UTC  
> Updated at: 2023-06-16 15:32:52 UTC  
> Url: https://github.com/boostorg/multi_index/issues/23  

Hints improve performance. Sometimes we have more than one hint available before insert, and not using them all is a waste of time and power.  
I figure the insert would have to be on the multi_index object itself and not a specific index.

---

## Comment 1

> Username: Delta-dev-99  
> Created at: 2023-06-16 15:32:51 UTC  
> Url: https://github.com/boostorg/multi_index/issues/23#issuecomment-1594878547  

This would be very helpful for performance-critical code. And makes sense to be able to do so.
