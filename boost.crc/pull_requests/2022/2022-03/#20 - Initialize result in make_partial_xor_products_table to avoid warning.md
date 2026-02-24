# #20 Initialize result in make_partial_xor_products_table to avoid warning [Closed]

> Username: FreddyFunk  
> Created at: 2022-03-12 17:29:25 UTC  
> Updated at: 2023-09-27 17:34:11 UTC  
> Closed at: 2023-09-27 17:34:10 UTC  
> Url: https://github.com/boostorg/crc/pull/20  

This fixes the following warning when compiling with MSVC:  
  
`crc.hpp(984) : warning C4701: potentially uninitialized local variable 'result' used`  
  
This PR is identical to https://github.com/boostorg/crc/pull/6.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-09-27 17:34:11 UTC  
> Url: https://github.com/boostorg/crc/pull/20#issuecomment-1737815041  

Merged #6.

---
