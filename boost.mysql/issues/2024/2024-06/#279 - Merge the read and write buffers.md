# #279 - Merge the read and write buffers [Open]

> Username: anarthal  
> Created at: 2024-06-06 17:34:19 UTC  
> Updated at: 2024-06-06 17:34:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/279  

All operations work in a half-duplex fashion, so they don't need separate buffers. Pipelines have their own buffer, so they don't need this, either. This can reduce memory consumption and make #279 easier.
