# #215 - Invalid include of hash_fwd [Closed]

> Username: Flamefire  
> Created at: 2021-11-11 12:20:52 UTC  
> Updated at: 2021-11-11 13:41:52 UTC  
> Closed at: 2021-11-11 13:27:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/215  

https://github.com/boostorg/filesystem/commit/2e6cc63e653e5f99f191e8733013617521dc69ab introduced a bug:  
It uses hash_range in an inline function but has only hash_fwd.hpp included. I.e. a full use with only the forward declaration header included.  
  
When using the compiler flag `-fkeep-inline` it leads now to undefined reference...

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-11 13:27:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/215#issuecomment-966301655  

This is not a bug. Users are supposed to include `boost/container_hash/hash.hpp` before using hash functions. What Boost.Filesystem defines is a plug-in for Boost.ContainerHash.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-11-11 13:41:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/215#issuecomment-966311940  

As for `-fkeep-inline`, I've committed a workaround that should fix the linking issue.
