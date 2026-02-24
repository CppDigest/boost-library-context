# #256 - hash_value overloads can probably be removed now [Closed]

> Username: pdimov  
> Created at: 2023-10-17 18:38:15 UTC  
> Updated at: 2024-09-30 21:39:27 UTC  
> Closed at: 2024-09-30 21:39:27 UTC  
> Url: https://github.com/boostorg/container/issues/256  

ContainerHash now has generic support for ranges, contiguous ranges and unordered ranges, so `boost::hash` should work for the containers as-is, without the need for library-provided `hash_value` overloads.  
  
I don't see tests exercising `boost::hash` though, so it might be a good idea to add those first.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-30 21:39:27 UTC  
> Url: https://github.com/boostorg/container/issues/256#issuecomment-2384189875  

hash_value overload was removed in commit https://github.com/boostorg/container/commit/0f448277c5a206f4872b6073dbe8ad7e754fd219
