# #1029 has_denorm_now [Merged]

> Username: ryanelandt  
> Created at: 2023-09-11 18:11:57 UTC  
> Updated at: 2023-10-30 08:37:30 UTC  
> Merged at: 2023-10-30 08:37:30 UTC  
> Closed at: 2023-10-30 08:37:30 UTC  
> Url: https://github.com/boostorg/math/pull/1029  

As pointed out in #1028, `std::numeric_limits<T>::has_denorm` has been deprecated in C++23.  
  
This PR refactors `get_smallest_value` to no longer use `has_denorm`. A new method `has_denorm_now` is created that checks if denorms are supported at runtime by comparing the output of `boost::math::detail::get_smallest_value` with that of `boost::math::tools::min_value`. Most other instances of the compile time constant `has_denorm` are replaced by the runtime calculated version `has_denorm_now`.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-10-18 12:21:58 UTC  
> Url: https://github.com/boostorg/math/pull/1029#issuecomment-1768335848  

Thanks Matt, this looks good to me!  Any reason not to merge?

---
