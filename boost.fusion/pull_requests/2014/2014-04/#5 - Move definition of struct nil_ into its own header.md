# #5 Move definition of struct nil_ into its own header [Merged]

> Username: ericniebler  
> Created at: 2014-04-23 23:52:53 UTC  
> Updated at: 2014-04-24 00:55:18 UTC  
> Merged at: 2014-04-24 00:55:18 UTC  
> Closed at: 2014-04-24 00:55:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/5  

This is to work around a problem in `list/detail/empty_impl.hpp`, where the nvcc compiler was complaining about `nil_` being incomplete.

---
