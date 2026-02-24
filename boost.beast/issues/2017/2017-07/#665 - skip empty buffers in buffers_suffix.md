# #665 - skip empty buffers in buffers_suffix [Open]

> Username: vinniefalco  
> Created at: 2017-07-17 16:09:11 UTC  
> Updated at: 2019-04-19 22:26:15 UTC  
> Url: https://github.com/boostorg/beast/issues/665  

This will make it easier to reason about code and also solve the problem where consume() does nothing when the first buffer is empty.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-19 02:11:41 UTC  
> Url: https://github.com/boostorg/beast/issues/665#issuecomment-484745665  

This needs to be reviewed, it might not be a problem anymore.

---

## Comment 2

> Username: djarek  
> Created at: 2019-04-19 22:26:15 UTC  
> Url: https://github.com/boostorg/beast/issues/665#issuecomment-485028666  

This would make moving and iterating more expensive. Algorithms that consume buffer sequences already have to (implicitly) deal with a buffer being to small to use, so an empty buffer should be just fine.
