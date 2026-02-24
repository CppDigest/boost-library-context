# #3047 - async_base::complete dispatches handler without using the operation's associated executor [Open]

> Username: ashtum  
> Created at: 2025-10-21 10:34:51 UTC  
> Updated at: 2026-01-05 06:05:17 UTC  
> Url: https://github.com/boostorg/beast/issues/3047  

Currently, `async_base::complete` passes the handler to `dispatch`, which may not have an associated executor. However, the operation itself (`async_base`) is tied to the stream's default executor. This can lead to the handler being executed on an unexpected or incorrect execution context:  
  
https://github.com/boostorg/beast/blob/63f3d3f3930288726b5862f24728ed9d418d080a/include/boost/beast/core/async_base.hpp#L421-L425

---

## Comment 1

> Username: albxy  
> Created at: 2026-01-05 06:05:17 UTC  
> Url: https://github.com/boostorg/beast/issues/3047#issuecomment-3709055016  

How to fix it?
