# #482 [srs] Add missing cstring include. [Merged]

> Username: e-kwsm  
> Created at: 2018-05-14 06:39:35 UTC  
> Updated at: 2018-06-04 12:39:26 UTC  
> Merged at: 2018-06-04 12:09:04 UTC  
> Closed at: 2018-06-04 12:09:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/482  

`geometry/algorithms/detail/relate/result.hpp` uses `memset` and `memcpy` but `cstring` is not included.

---

## Comment 1

> Username: e-kwsm  
> Created_at: 2018-05-14 06:55:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/482#issuecomment-388714905  

BTW, there are variants of `memcpy`, `::memcpy`, `std::memcpy`, and `::memset`, which should be qualified as `std::`.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2018-06-04 12:07:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/482#issuecomment-394331970  

Thanks!  
Actually the code would be more clear if these functions were replaced with `std::copy` and `std::fill` which internally should call `memcpy` and `memset` in this case.

---
