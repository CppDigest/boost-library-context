# #379 Remove boost.predef dependency [Merged]

> Username: mborland  
> Created at: 2021-10-09 08:56:30 UTC  
> Updated at: 2021-11-21 15:25:44 UTC  
> Merged at: 2021-10-23 11:30:04 UTC  
> Closed at: 2021-10-23 11:30:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/379  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-09 15:49:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/379#pullrequestreview-775588827  

📁 include/boost/multiprecision/detail/endian.hpp

```diff
  19 |+ #  if __has_include(<bit>)
  20 |+ #    include <bit>
  21 |+ #    define BOOST_MP_ENDIAN_BIG_BYTE (std::endian::native == std::endian::big)
```

> Username: jzmaddock  
> Created_at: 2021-10-09 15:49:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/379#discussion_r725503334  

Sadly, I don't believe this will work since the types and values used here are not usable in a preprocessor conditional?

> Username: mborland  
> Created_at: 2021-10-09 17:00:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/379#discussion_r725511227  

You are correct. `std::endian` is just an `enum` aliasing the byte order macros (with libstdc++ anyway).


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-09 15:51:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/379#pullrequestreview-775588940  

📁 include/boost/multiprecision/cpp_int/import_export.hpp

```diff
   7 | #define BOOST_MP_CPP_INT_IMPORT_EXPORT_HPP
   8 | 
   9 |+ #include <climits>
```

> Username: jzmaddock  
> Created_at: 2021-10-09 15:51:12 UTC  
> Updated_at: 2021-10-09 15:51:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/379#discussion_r725503542  

Just curious, why the extra #include's here and elsewhere, were we getting these accidentally via predef/endian?

> Username: mborland  
> Created_at: 2021-10-09 17:02:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/379#discussion_r725511524  

I think they were getting pulled in from somewhere else in boost. My editor started complaining about undefined references so these cleared up the red squiggles.


---

## Comment 3

> Username: mborland  
> Created_at: 2021-10-11 07:17:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/379#issuecomment-939755103  

Here and in Math CircleCI says running is paused due to concurrency limits. Did we run out of credits again?

---
