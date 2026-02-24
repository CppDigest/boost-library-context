# #504 Add `BOOST_FALLTHROUGH` for MSVC [Open]

> Username: Flamefire  
> Created at: 2024-11-10 10:49:24 UTC  
> Updated at: 2025-07-04 18:06:11 UTC  
> Url: https://github.com/boostorg/config/pull/504  

MSVC supports this attribute for C++17 and higher since VS 2017: https://learn.microsoft.com/en-us/cpp/cpp/attributes?view=msvc-170#fallthrough  
  
Not sure if it is worth adding a check for `_MSC_VER >= 1910` as it isn't done for `BOOST_ATTRIBUTE_UNUSED` either

---

## Comment 1

> Username: Flamefire  
> Created_at: 2025-07-04 18:06:11 UTC  
> Url: https://github.com/boostorg/config/pull/504#issuecomment-3036986342  

@glenfe Can we include this in 1.89?

---
