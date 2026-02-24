# #172 Make build `-Wundef` clean [Merged]

> Username: Flamefire  
> Created at: 2022-07-07 08:06:19 UTC  
> Updated at: 2022-07-07 12:07:35 UTC  
> Merged at: 2022-07-07 10:17:26 UTC  
> Closed at: 2022-07-07 10:17:26 UTC  
> Url: https://github.com/boostorg/type_traits/pull/172  

Use `#ifdef __cpp_noexcept_function_type` instead of  
`#if __cpp_noexcept_function_type` to avoid `-Wundef` warnings.  
Also add that flag to the test flags to detect those on CI.

---
