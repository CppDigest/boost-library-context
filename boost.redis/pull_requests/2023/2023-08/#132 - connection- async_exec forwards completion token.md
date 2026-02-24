# #132 connection: async_exec forwards completion token [Merged]

> Username: cbodley  
> Created at: 2023-08-02 15:47:21 UTC  
> Updated at: 2023-08-02 20:06:55 UTC  
> Merged at: 2023-08-02 20:06:27 UTC  
> Closed at: 2023-08-02 20:06:27 UTC  
> Url: https://github.com/boostorg/redis/pull/132  

async operations should support move-only completion handlers. forward the CompletionToken argument to avoid an unnecessary copy  
  
Fixes: #131

---

## Comment 1

> Username: mzimbres  
> Created_at: 2023-08-02 20:06:54 UTC  
> Url: https://github.com/boostorg/redis/pull/132#issuecomment-1662899856  

Thanks.

---
