# #290 - boost::hash<container::string> doesn't match boost::hash<std::string_view> [Closed]

> Username: pdimov  
> Created at: 2024-09-29 00:58:48 UTC  
> Updated at: 2024-09-30 14:20:42 UTC  
> Closed at: 2024-09-30 14:00:16 UTC  
> Url: https://github.com/boostorg/container/issues/290  

A user has reported a regression caused by the mismatch in the hash values of `container::string` and `std::string_view`, which breaks transparent hash lookups.  
  
This is caused by the `hash_value` overload here  
  
https://github.com/boostorg/container/blob/e08a064896048daa306b82d7525e5b524051fb42/include/boost/container/string.hpp#L3549  
  
which I see has already been removed in develop, so this will hopefully be fixed in the next release.  
  
But it would be good to add a test that the hash values of `container::string` and its corresponding `std::string_view` match, to avoid similar things happening in the future.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-09-30 14:20:41 UTC  
> Url: https://github.com/boostorg/container/issues/290#issuecomment-2383350305  

Thanks.  
  
It's not incorrect to test `boost::hash_value(x)`, but it will be better to test `boost::hash<X>()(x)` instead, because that's what user code ends up using through the unordered containers. In principle these two do the same thing today, but who knows what the future will bring.
