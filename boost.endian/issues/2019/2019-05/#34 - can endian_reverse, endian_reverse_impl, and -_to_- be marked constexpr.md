# #34 - can endian_reverse, endian_reverse_impl, and *_to_* be marked constexpr? [Closed]

> Username: saurik  
> Created at: 2019-05-30 20:21:46 UTC  
> Updated at: 2019-10-15 13:43:38 UTC  
> Closed at: 2019-10-15 13:43:38 UTC  
> Url: https://github.com/boostorg/endian/issues/34  

I would love to be able to use the endian swapping routines in contexts where I need to use a constexpr, and it seems like these routines all fit the constexpr premise perfectly (as they are pure math based on a static property of the architecture). FWIW, I tried marking these functions as constexpr, and it worked great ;P.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-30 23:15:49 UTC  
> Url: https://github.com/boostorg/endian/issues/34#issuecomment-497518717  

Unfortunately at least on MSVC the byteswap intrinsics used by endian_reverse_impl aren't constexpr.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-10-12 10:28:18 UTC  
> Updated at: 2019-10-12 10:28:31 UTC  
> Url: https://github.com/boostorg/endian/issues/34#issuecomment-541311606  

I've made `endian_reverse`, `conditional_reverse` and `*_to_*` `constexpr` on gcc and clang. (On the develop branch.)
