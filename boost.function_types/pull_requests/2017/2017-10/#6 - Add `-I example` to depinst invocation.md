# #6 Add `-I example` to depinst invocation [Merged]

> Username: pdimov  
> Created at: 2017-10-25 22:15:49 UTC  
> Updated at: 2017-10-26 02:22:51 UTC  
> Merged at: 2017-10-26 02:03:25 UTC  
> Closed at: 2017-10-26 02:03:25 UTC  
> Url: https://github.com/boostorg/function_types/pull/6  

I removed scanning `example` by default in `depinst.py` and instead added an option, `-I/--include`, to specify an additional directory.

---
