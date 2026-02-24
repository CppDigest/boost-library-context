# #284 - Replace axis::traits with uniform wrapper [Open]

> Username: HDembinski  
> Created at: 2020-07-05 06:47:14 UTC  
> Updated at: 2020-10-03 10:12:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/284  

Perhaps histogram could wrap external classes with an interface class that supplements all optional interface. If this works, it  would greatly simplify the library compared to the current approach with axis::traits.

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-10-03 10:12:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/284#issuecomment-703080404  

This would have to be a CRTP class, so that implementations in the user-provided class override the default implementations in the interface class.
