# #749 - lru_cache does not allow to update current value in the cache [Closed]

> Username: vertexodessa  
> Created at: 2017-10-30 05:58:03 UTC  
> Updated at: 2018-12-30 12:16:43 UTC  
> Closed at: 2018-12-30 12:15:45 UTC  
> Url: https://github.com/boostorg/compute/issues/749  

https://github.com/boostorg/compute/blob/master/include/boost/compute/detail/lru_cache.hpp#L66  
"insert" function silently returns if the cache already contains the key, without updating the current value.  
  
There is also no API to remove the value if the cache contains an item associated with the key, so it looks like it's hard to work around this limitation.

---

## Comment 1

> Username: Ulfgard  
> Created at: 2017-10-30 08:01:30 UTC  
> Url: https://github.com/boostorg/compute/issues/749#issuecomment-340369404  

a workaround for this limitation is to use get after insertion as this will update the list. This costs an additional map lookup, but should be fine performance-wise.

---

## Comment 2

> Username: vertexodessa  
> Created at: 2017-10-30 08:03:39 UTC  
> Updated at: 2017-11-21 20:45:54 UTC  
> Url: https://github.com/boostorg/compute/issues/749#issuecomment-340369816  

Sorry about the ambiguity in the description.  
I think using get will not solve the issue, as it won't help to update the value of the key  
cache.insert(1,1);  
cache.insert(1,2);  
cache.get(1);  // what would you expect this to return? (currently the value is not updated and it returns 1)

---

## Comment 3

> Username: jszuppe  
> Created at: 2018-12-30 12:15:45 UTC  
> Url: https://github.com/boostorg/compute/issues/749#issuecomment-450556858  

This class was designed for storing compiled OpenCL programs (kernels). That's why there's no need to update value on `insert()`. It assumes that (key, values) pairs are unique, which means you can't have pairs (1,1) and (1,2). Also, it's in `detail::` namespace, which means it's not a public API.   
  
I'm closing the issue, marking not-a-bug. Please reopen if this behaviour causes problems in Boost.Compute, for example: bad program is run.
