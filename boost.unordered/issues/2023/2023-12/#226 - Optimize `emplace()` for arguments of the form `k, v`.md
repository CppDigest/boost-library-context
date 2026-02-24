# #226 - Optimize `emplace()` for arguments of the form `k, v` [Closed]

> Username: k3DW  
> Created at: 2023-12-27 04:17:42 UTC  
> Updated at: 2024-02-12 16:24:50 UTC  
> Closed at: 2024-02-12 16:24:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/226  

We can defer the `value_type` construction until we're sure we will emplace.
