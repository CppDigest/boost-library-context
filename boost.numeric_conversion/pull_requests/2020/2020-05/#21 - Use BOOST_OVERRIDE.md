# #21 Use BOOST_OVERRIDE [Merged]

> Username: EugeneZelenko  
> Created at: 2020-05-24 23:36:21 UTC  
> Updated at: 2020-06-15 18:09:33 UTC  
> Merged at: 2020-06-15 17:53:49 UTC  
> Closed at: 2020-06-15 17:53:49 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/21  

Use BOOST_OVERRIDE to fix GCC -Wsuggest-override and Clang-tidy modernize-use-override warnings.  
Alphabetical order of STL headers.

---

## Review 1 [Changes requested]

> Username: brandon-kohn  
> Created_at: 2020-06-15 17:28:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/numeric_conversion/pull/21#pullrequestreview-430848982  

Thanks for the PR.

📁 include/boost/numeric/conversion/converter_policies.hpp

```diff
  11 | #define BOOST_NUMERIC_CONVERSION_CONVERTER_POLICIES_FLC_12NOV2002_HPP
  12 | 
  13 |+ #include <functional>
```

> Username: brandon-kohn  
> Created_at: 2020-06-15 17:27:14 UTC  
> Updated_at: 2020-06-15 17:28:58 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/21#discussion_r440332839  

Why was this moved?

---

📁 include/boost/numeric/conversion/converter_policies.hpp

```diff
 138 | 
 140 |-     virtual const char * what() const BOOST_NOEXCEPT_OR_NOTHROW
 139 |+     const char * what() const BOOST_NOEXCEPT_OR_NOTHROW BOOST_OVERRIDE
```

> Username: brandon-kohn  
> Created_at: 2020-06-15 17:28:19 UTC  
> Updated_at: 2020-06-15 17:52:39 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/21#discussion_r440333448  

The BOOST_OVERRIDE is great.


---

## Comment 2

> Username: EugeneZelenko  
> Created_at: 2020-06-15 17:34:24 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/21#issuecomment-644270841  

Clang-tidy modernize-use-override warnings complains when both `virtual` and `override` are used. `virtual` is not required when virtual function is redefined, so `BOOST_OVERRIDE` is enough to understand that fact.

---
