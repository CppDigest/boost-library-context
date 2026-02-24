# #138 - feature request: std::print support for boost::system::error_code [Open]

> Username: ribbon-otter  
> Created at: 2026-01-12 05:58:35 UTC  
> Updated at: 2026-01-12 13:15:31 UTC  
> Url: https://github.com/boostorg/system/issues/138  

I think it would be nice if `boost::system::error_code` had `std::print` support. From analogy of the ostream interface, the `std::formatter<boost::system::error_code>` would be defined in terms of `error_code::to_string()` but perhaps we would want to print a more human readable value by default, like `error_code::what()`

---

## Comment 1

> Username: pdimov  
> Created at: 2026-01-12 12:08:11 UTC  
> Url: https://github.com/boostorg/system/issues/138#issuecomment-3738249235  

There's a proposal for adding `std::format` support to `std::error_code`:  
  
https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3395r5.html  
  
but it's not finalized or accepted yet.  
  
We probably want to see what the committee will do first, because if we go ahead and add our own, and they later change it, this will break code for our users if we also change to match.

---

## Comment 2

> Username: pdimov  
> Created at: 2026-01-12 13:15:31 UTC  
> Url: https://github.com/boostorg/system/issues/138#issuecomment-3738501115  

Although... I suppose that it's reasonably certain by this point that `{}` should output the same as `operator<<`, so we can add that at least.
