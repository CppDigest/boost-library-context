# #45 string_view interoperability [Merged]

> Username: alandefreitas  
> Created at: 2023-01-12 21:25:47 UTC  
> Updated at: 2023-01-19 22:57:33 UTC  
> Merged at: 2023-01-19 22:57:32 UTC  
> Closed at: 2023-01-19 22:57:33 UTC  
> Url: https://github.com/boostorg/static_string/pull/45  

fix #26  
  
This PR includes interoperability with `std::string_view` and `core::string_view`.  
  
- Interoperability with `std::string_view` is enabled not only for standalone mode now. This means the string_view operations are now independent of the default `static_strings::basic_string_view` type.  
- Interoperability with `core::string_view` now allows conversions from and to more types than `boost::string_view`. This is especially useful when `std::string_view` is unavailable.  
- Interoperability with `boost::string_view` is maintained. Removing it would break things. For instance, any type `T` convertible to `boost::string_view`, like we have in unit tests.  
- Interoperability with string-like types is used when no other string_view-like type is available. For instance, in our test "Clang 3.8: C++11 Standalone" that currently exists in CI, which has no string_view-type available. The question now is why these tests weren't failing before.  
- The comparison operators and implicit conversions are now available for all these string_view types  
- No explicit conversion to `std::string` has been implemented as the library does not include `<string>`

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2023-01-12 21:26:40 UTC  
> Updated_at: 2023-01-12 21:26:59 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1381012082  

@timblechmann Does this solve your problem?

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-01-19 14:53:11 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397099759  

@pdimov Did I break anything here? :laughing:

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-01-19 15:40:53 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397179892  

I have no idea.  
  
You should use the Boost.Config macro for the detection of `<string_view>` instead of custom logic.  
  
Why are the tests predicated on "HAS_STRING_VIEW"? Why not always use `core::string_view` as the `string_view_type`?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2023-01-19 16:20:25 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397239877  

Good questions. We probably need a better name for the macro.  
  
> You should use the Boost.Config macro for the detection of <string_view> instead of custom logic.  
  
It uses the Boost.Config macro at first  
  
https://github.com/alandefreitas/static_string/blob/3155dc8f132ce5eacc1dfb1016cee05b5597cd1d/include/boost/static_string/config.hpp#L167-L168  
  
and then there's the logic with `__has_include`, which is intended to work for standalone usage.  
  
https://github.com/alandefreitas/static_string/blob/3155dc8f132ce5eacc1dfb1016cee05b5597cd1d/include/boost/static_string/config.hpp#L169-L170  
  
The above includes the header but doesn't set the macro yet because there are cases where the header is available but the class is not defined. So there's a separate `__cpp_lib_string_view >= 201606L` test to set the `HAS_STD_STRING_VIEW` macro:  
  
https://github.com/alandefreitas/static_string/blob/3155dc8f132ce5eacc1dfb1016cee05b5597cd1d/include/boost/static_string/config.hpp#L176-L178  
  
> Why are the tests predicated on "HAS_STRING_VIEW"? Why not always use core::string_view as the string_view_type?  
  
This macro should probably be `HAS_ANY_STRING_VIEW` or something. It exists because of the standalone library. The standalone library is tested on C++11 and C++14, where no string_view type is available at all.   
  
In that case, the member functions constrained on `string_like` types (constructors, assignment, and most modifiers) need to work without any `string_view` type available. In this case, if `HAS_ANY_STRING_VIEW` is not set, I fallback on an `is_string_like` trait that uses that requires `data()` and `size()`.  
  
https://github.com/alandefreitas/static_string/blob/3155dc8f132ce5eacc1dfb1016cee05b5597cd1d/include/boost/static_string/static_string.hpp#L153-L166

---

## Comment 5

> Username: pdimov  
> Created_at: 2023-01-19 16:56:07 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397301261  

The `__has_include` part is not guaranteed to work, because including the header can give an `#error` in pre-C++17.

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2023-01-19 17:02:41 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397310872  

> The __has_include part is not guaranteed to work, because including the header can give an #error in pre-C++17.  
  
What's the best alternative for the standalone library? Replicating the logic for `BOOST_NO_CXX17_HDR_STRING_VIEW`? This could be huge but it might be the only way then.

---

## Comment 7

> Username: pdimov  
> Created_at: 2023-01-19 17:04:52 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397314134  

The best alternative is to drop the standalone library, although I suppose you could also check `__cplusplus` in addition to `__has_include`. But this won't work under MSVC, where you'll need to check `_MSVC_LANG` or something like that.

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2023-01-19 17:14:39 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397329431  

> The best alternative is to drop the standalone library,   
  
Yes. That's outside the scope of my power.  
  
> although I suppose you could also check __cplusplus in addition to __has_include. But this won't work under MSVC, where you'll need to check _MSVC_LANG or something like that.  
  
I saw that boost.config relies on `#if (_MSC_VER < 1911) || (_MSVC_LANG < 201703)`. What's the problem with MSVC? They didn't define __cplusplus properly back then?

---

## Comment 9

> Username: pdimov  
> Created_at: 2023-01-19 17:15:46 UTC  
> Url: https://github.com/boostorg/static_string/pull/45#issuecomment-1397330824  

They still don't.

---
