# #326 - Memory leak in debug_parent_print [Closed]

> Username: jwakely  
> Created at: 2018-07-18 14:57:17 UTC  
> Updated at: 2018-07-27 17:15:24 UTC  
> Closed at: 2018-07-27 17:15:24 UTC  
> Url: https://github.com/boostorg/build/issues/326  

https://github.com/boostorg/build/blob/0c6d016/src/engine/debugger.c#L1520 creates a list and then doesn't free it before returning. I think there's a `list_free(result)` missing.
