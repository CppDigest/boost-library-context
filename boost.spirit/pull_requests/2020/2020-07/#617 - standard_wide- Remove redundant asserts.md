# #617 standard_wide: Remove redundant asserts [Merged]

> Username: Kojoley  
> Created at: 2020-07-16 18:26:04 UTC  
> Updated at: 2020-07-18 13:08:29 UTC  
> Merged at: 2020-07-18 13:08:26 UTC  
> Closed at: 2020-07-18 13:08:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/617  

> For all functions described in this subclause that accept an argument of type wint_t, the value shall  
> be representable as a wchar_t or shall equal the value of the macro WEOF. If this argument has any  
> other value, the behavior is undefined.  
  
There is no way to produce a `wint_t` value non-representable as `wchar_t` with `std::char_traits<wchar_t>::to_int_type`.  
  
Closes #616

---
