# #64 Assert refcount before decrement [Merged]

> Username: WKarel  
> Created at: 2016-04-28 11:43:23 UTC  
> Updated at: 2016-09-29 11:48:56 UTC  
> Merged at: 2016-09-29 11:48:46 UTC  
> Closed at: 2016-09-29 11:48:46 UTC  
> Url: https://github.com/boostorg/python/pull/64  

Due to programming errors, the reference count may already be zero before trying to decrement it. If nevertheless, the reference count is decremented, the PyObject will be left with a large reference count, pointing to already destroyed memory, possibly resulting in a hard-to-track segmentation fault. This fix introduces assertions that the reference count is above zero before decrementing it, which provide useful indications of this programming error in debug-builds (even when using a release-build of Python).  
  
This [Gist](https://gist.github.com/WKarel/ff9eff5be792e0b4af2f4d12098a1bcb) shows how to produce a situation in which the assertions are helpful.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2016-09-29 11:48:56 UTC  
> Url: https://github.com/boostorg/python/pull/64#issuecomment-250444399  

Thanks for the patch !

---
