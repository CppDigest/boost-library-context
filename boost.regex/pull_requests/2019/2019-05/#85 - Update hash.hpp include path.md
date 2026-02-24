# #85 Update hash.hpp include path [Merged]

> Username: Mike-Devel  
> Created at: 2019-05-11 17:13:35 UTC  
> Updated at: 2019-05-13 17:22:47 UTC  
> Merged at: 2019-05-13 17:22:47 UTC  
> Closed at: 2019-05-13 17:22:47 UTC  
> Url: https://github.com/boostorg/regex/pull/85  

The hash functions now reside in `boost/container_hash/hash.hpp`  
  
`<boost/functional/hash.hpp>` is just forwarding to the new location and using the old location might lead to confusion about the dependencies in some circumstances (e.g. https://github.com/boostorg/regex/pull/83)

---
