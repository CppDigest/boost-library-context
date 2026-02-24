# #225 - Optimize `emplace()` for exactly a `value_type` or `init_type` argument [Closed]

> Username: k3DW  
> Created at: 2023-12-27 04:15:48 UTC  
> Updated at: 2023-12-31 17:56:16 UTC  
> Closed at: 2023-12-31 17:56:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/225  

Creating an intermediate value can be bypassed since we're already given one.
