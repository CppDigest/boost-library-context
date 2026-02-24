# #134 Add DISABLE_VARIADIC_VECTOR and SFINAE for c++11 tuple element construction [Merged]

> Username: vtnerd  
> Created at: 2016-07-22 23:56:50 UTC  
> Updated at: 2016-07-31 06:58:17 UTC  
> Merged at: 2016-07-31 06:58:17 UTC  
> Closed at: 2016-07-31 06:58:17 UTC  
> Url: https://github.com/boostorg/fusion/pull/134  

@flast for review, and @K-ballo for compile-time performance suggestions. `BOOST_FUSION_DISABLE_VARIADIC_VECTOR` is undocumented for now. Requiring `BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS` for variadic vector does **not** increase the minimum necessary versions of Gcc, Clang or MSVC, but _may_ for other compilers. Updates to vector to fix the various bugs are upcoming next.   
### Issues Fixed  
  
C++11 `fusion::tuple` construction from elements is never disabled. This changes the `boost::is_convertible` and `boost::is_constructible` results, and introduces conversion from fusion sequence behavior. This patch makes the C++03 and C++11 `fusion::tuple` nearly identical in behavior (see tests). Construction differences from std::tuple are listed in tests too.  
### Compile Performance Timings  
  
All of the timings were done on an (ancient) Intel Core 2 Duo T5250 (1.50 GHz 2MB cache) in 64-bit Gentoo Linux using the `time` application. The recursive version required increasing the template depth.  
#### Construction of a `fusion::tuple` of size 1000  
  
|  | Boost 1.61 | Develop | Develop + Recursive and_ | Develop + At Once and_ |  
| --- | --- | --- | --- | --- |  
| Gcc 4.9 | 29.082s | 28.146s | 38.937s | 28.172s |  
| Clang 3.7 | 6.836s | 6.809s | 8.350s | 6.837s |  
#### `boost::is_constructible` with a `fusion::tuple`of size 1000  
  
On Boost 1.61 and Develop the call passed, while on the other two it failed. The recursive and_ failed on the first element check (optimal), while the at-once and_ had to check all elements.  
  
|  | Boost 1.61 | Develop | Develop + Recursive and_ | Develop + At Once and_ |  
| --- | --- | --- | --- | --- |  
| Gcc 4.9 | 4.924s | 4.859s | 4.815s | 4.930s |  
| Clang 3.7 | 2.732s | 2.762s | 2.754s | 2.786s |  
#### Conclusion  
  
The at-once and_ adds little overhead in the usual and worst-cases. The other option would be to remove the universal constructor in favor of the C++03 style, but this would add an additional move/copy that cannot be elided by the frontend (but might be optimized away due to inlining/LTO).

---

## Comment 1

> Username: K-ballo  
> Created_at: 2016-07-23 01:12:51 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71963999  

Nice implementation! I believe the empty set should return `true` instead, what's the reason for that first `true_type` in the specialization?

---

## Comment 2

> Username: Flast  
> Created_at: 2016-07-23 04:14:09 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71966603  

I think, just a short-circuit.

---

## Comment 3

> Username: Flast  
> Created_at: 2016-07-23 04:24:21 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71966699  

`vector/detail/config.hpp` affects `fuson::vector`, it should be in [tuple/tuple_fwd.hpp](https://github.com/boostorg/fusion/blob/develop/include/boost/fusion/tuple/tuple_fwd.hpp#L14).

---

## Comment 4

> Username: vtnerd  
> Created_at: 2016-07-23 22:16:10 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71979054  

The first `true_type` in the specialization prevents it from matching the empty-set case. Returning true on the empty-set might make more sense. I chose false because `std::tuple` disables the variadic constructions if zero elements are are provided, so this implementation automatically disables the constructor without an additional check. Perhaps that check should be explicit?

---

## Comment 5

> Username: vtnerd  
> Created_at: 2016-07-23 22:21:23 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71979103  

Moved `NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS` check to tuple_fwd.hpp.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2016-07-23 22:23:17 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71979115  

...should I add `NO_CXX11_VARIADIC_TEMPLATES` to that tuple_fwd.hpp? Technically that is another requirement of variadic tuple directly.

---

## Comment 7

> Username: K-ballo  
> Created_at: 2016-07-23 22:32:59 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71979177  

An explicit check would make more sense to me

---

## Comment 8

> Username: vtnerd  
> Created_at: 2016-07-23 22:56:42 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71979328  

`fusion::detail::and_<>::value` is now `true`, and a `sizeof...(U) >= 1` was added to the SFINAE check for tuple.

---

## Comment 9

> Username: Flast  
> Created_at: 2016-07-24 07:38:17 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71983591  

nope. `!defined(BOOST_FUSION_HAS_VARIADIC_VECTOR)` induces `NO_CXX11_VARIADIC_TEMPLATES`.

---

## Comment 10

> Username: vtnerd  
> Created_at: 2016-07-24 17:21:19 UTC  
> Updated_at: 2016-07-26 19:03:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r71992058  

Yes, but should tuple check this separately since it also requires it? I only bring it up because my next patch will add the `NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS` requirement to `fusion::vector` too.

---

## Comment 11

> Username: Flast  
> Created_at: 2016-07-28 11:50:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#discussion_r72607842  

Sorry for late reply.  
  
> Yes, but should tuple check this separately since it also requires it? I only bring it up because my next patch will add the `NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS` requirement to `fusion::vector` too.  
  
I think the check is unnecessary, but acceptable. I worry about "too much" or "insufficient".

---

## Comment 12

> Username: Flast  
> Created_at: 2016-07-30 07:28:46 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#issuecomment-236350695  

I'll merge this PR if nobody has comments.

---

## Comment 13

> Username: djowel  
> Created_at: 2016-07-30 20:33:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/134#issuecomment-236388520  

Looks good to me. Nice work, @vtnerd!

---
