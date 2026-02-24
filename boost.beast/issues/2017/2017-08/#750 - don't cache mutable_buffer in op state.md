# #750 - don't cache mutable_buffer in op state [Closed]

> Username: vinniefalco  
> Created at: 2017-08-22 17:43:26 UTC  
> Updated at: 2017-09-04 02:57:47 UTC  
> Closed at: 2017-09-04 02:57:47 UTC  
> Url: https://github.com/boostorg/beast/issues/750  

composed operation states do not need to cache the mutable buffer sequence returned from DynamicBuffer's prepare function, since initiating functions make a copy of the sequence (but not the underlying buffers).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-04 02:57:47 UTC  
> Url: https://github.com/boostorg/beast/issues/750#issuecomment-326855424  

Duplicate of #764
