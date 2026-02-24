# #1332 - Don't call allocator_traits<T>::construct [Closed]

> Username: vinniefalco  
> Created at: 2018-12-02 00:32:24 UTC  
> Updated at: 2019-05-20 16:49:30 UTC  
> Closed at: 2019-05-20 16:49:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1332  

`construct` should only be used by containers to construct a user-supplied `value_type`. I don't think there are any places in Beast that this is applicable to, so all current calls to `construct` should be replaced with ordinary construction.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-01-01 03:28:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1332#issuecomment-450707517  

This issue has been open for a while with no activity, has it been resolved?
