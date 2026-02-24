# #350 Use correct sizeof in malloc call [Merged]

> Username: jwakely  
> Created at: 2018-10-10 12:47:21 UTC  
> Updated at: 2021-10-02 21:19:35 UTC  
> Merged at: 2018-10-10 15:30:38 UTC  
> Closed at: 2018-10-10 15:30:38 UTC  
> Url: https://github.com/boostorg/build/pull/350  

This is allocating space for `nel` objects of type `ITEM*` so it should use `sizeof(ITEM*)` not `sizeof(ITEM**)`.  
  
In practice the values are the same, but using the correct type is better anyway, and now matches the same calculation in the `memset` call in the following statement.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-10-10 15:28:02 UTC  
> Url: https://github.com/boostorg/build/pull/350#issuecomment-428617778  

This looks good to me.

---
