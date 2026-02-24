# #402 Remove more long doubles [Merged]

> Username: evanmiller  
> Created at: 2020-07-15 14:10:39 UTC  
> Updated at: 2020-07-17 08:03:23 UTC  
> Merged at: 2020-07-17 08:03:23 UTC  
> Closed at: 2020-07-17 08:03:23 UTC  
> Url: https://github.com/boostorg/math/pull/402  

Eliminate unwanted long doubles from `unchecked_factorial` as well as `make_big_value`. The behavior of both functions should be identical (since the values were demoted to back to a `double` anyway) but these changes will remove references to `long double` from compiled code that doesn't want it.  
  
See #398.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-15 18:13:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/402#pullrequestreview-449212977  

📁 include/boost/math/tools/big_constant.hpp

```diff
  39 |+ #else
  40 |+ typedef double largest_float;
  41 |+ #define BOOST_MATH_LARGEST_FLOAT_C(x) x
```

> Username: jzmaddock  
> Created_at: 2020-07-15 18:13:49 UTC  
> Url: https://github.com/boostorg/math/pull/402#discussion_r455247472  

Surely this breaks long double support for those that want it?

> Username: evanmiller  
> Created_at: 2020-07-15 19:30:23 UTC  
> Url: https://github.com/boostorg/math/pull/402#discussion_r455291569  

This `else` condition is met when double and long double are identical (`LDBL_DIG == DBL_DIG`) - so either would qualify as the largest float on those platforms. I do not believe this will have any run-time effect or break any compiles but I could be wrong (maybe if the result of `BOOST_MATH_LARGEST_FLOAT_C` is passed to a templated function?).  
  
I'm basically indifferent to this particular change - but it does remove the last of the `long double`s from my debug symbols.

> Username: jzmaddock  
> Created_at: 2020-07-17 08:02:52 UTC  
> Updated_at: 2020-07-17 08:02:53 UTC  
> Url: https://github.com/boostorg/math/pull/402#discussion_r456284633  

Ah yes, my bad: I was viewing the diff and the PP logic got a bit confused at least in my head! ;)


---
