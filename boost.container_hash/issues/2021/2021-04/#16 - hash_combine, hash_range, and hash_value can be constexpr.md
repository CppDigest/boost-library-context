# #16 - hash_combine, hash_range, and hash_value can be constexpr [Open]

> Username: saki7  
> Created at: 2021-04-01 05:16:27 UTC  
> Updated at: 2022-09-19 18:53:23 UTC  
> Url: https://github.com/boostorg/container_hash/issues/16  

<https://github.com/boostorg/container_hash/blob/develop/include/boost/container_hash/hash.hpp>  
  
Since most part of internal calculations are based on integer arithmetic and bitwise operations, I believe those three functions and all their variants can be marked `constexpr` today. Can someone please enlighten me, why the standard `std::hash<T>::operator()` and proposed `std::hash_combine()` in [P0814R2](https://wg21.link/p0814r2) is not `constexpr`? (backward-compatibility, or other reasons?)  
  
  
If we are to provide constexpr-ness, there are two cases of implementations:  
- When user is using standard later or equal to C++20  
  - In this case, non-constexpr intrinsic like `_rotl` (defined for `BOOST_FUNCTIONAL_HASH_ROTL32`) can be replaced with [`std::rotl`](https://en.cppreference.com/w/cpp/numeric/rotl)  
- When user is using C++14 to C++17 and one *prefers* constexpr  
  - The library can fallback to bitwise implementation (which is always `constexpr`)  
  - Currently Boost.ContainerHash provides no option for this case. I think new macro like `BOOST_FUNCTIONAL_HASH_USE_CONSTEXPR` is needed.  
  
refs: cplusplus/papers#253

---

## Comment 1

> Username: pdimov  
> Created at: 2022-09-19 10:14:53 UTC  
> Url: https://github.com/boostorg/container_hash/issues/16#issuecomment-1250828410  

The current implementation (on develop) can in principle be `constexpr`; it no longer uses rotations. The problem, however, is that we support forward-declaring `boost::hash_combine` and `boost::hash_range`, so that people can add `boost::hash` support for their types without having to include a ContainerHash header (which would require Boost headers to be physically present even if they are otherwise not used.)  
  
Since the forward declarations lack `constexpr`, it's not possible to later define the functions to be `constexpr`.  
  
This is specific to `boost::hash`, of course, and doesn't apply to `std::hash`. I would guess, however, that stdlib implementers will be opposed to marking the standard functions `constexpr`, because this would require them to be implemented in the header, rather than out of line (as they currently tend to be.) This will make further changes to the hash functions effectively impossible because changes will break binary compatibility as the hash functions will already have been inlined into user code.

---

## Comment 2

> Username: saki7  
> Created at: 2022-09-19 18:53:23 UTC  
> Url: https://github.com/boostorg/container_hash/issues/16#issuecomment-1251416693  

Since `std::hash` is a template and it is intended to be specialized for certain types, their implementations must exist inside the header anyways. Even when there exists some hash implementations which delegate the calculation to non-template function, users would silently face an incompatible hash value when those external functions change. I suppose that kind of situation is also a binary incompatibility issue, and there's possibility for that to happen regardless of being constexpr or not.  
  
So my question still persists: why are hash functions not constexpr in the first place, despite the fact that they are already specialized in compile-time context?
