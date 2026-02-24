# #248 - Change `is_avalanching` protocol to one based on `std::[true|false]_type` [Closed]

> Username: joaquintides  
> Created at: 2024-05-08 09:07:06 UTC  
> Updated at: 2024-05-30 18:04:20 UTC  
> Closed at: 2024-05-30 18:04:20 UTC  
> Url: https://github.com/boostorg/unordered/issues/248  

Currently, hash function avalanching is indicated by the mere presence of a nested `is_avalanching` typedef (like `is_transparent`). Change that to requiring that `is_avalanching` is  `std::[true|false]_type` (or derived from those), in the same vein as propagate traits for allocators.  
  
To keep backwards compatiblity, if `is_avalanching` is present but not (derived from) `std::[true|false]_type` , this must be taken as equivalent to `using is_avalanching = std::true_type`.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-08 12:12:10 UTC  
> Url: https://github.com/boostorg/unordered/issues/248#issuecomment-2100437921  

I'm not sure that we need to support anything other than `void` for backward compatibility as that's what's presently used and documented everywhere. Silently accepting any type and treating it as true makes it way too easy to introduce errors.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-05-08 12:15:18 UTC  
> Url: https://github.com/boostorg/unordered/issues/248#issuecomment-2100443133  

I think what we want is: `hash_is_avalanching<H>` is  
  
* derived from `std::true_type` when `std::is_void<H::is_avalanching>::value` is `true`  
* otherwise derived from `std::integral_constant<bool, H::is_avalanching::value>`  
  
in the case `H::is_avalanching` exists, of course.  
  
This accepts `boost::integral_constant<bool, B>` and other non-`std` integral constants such as `mpl::bool_`.
