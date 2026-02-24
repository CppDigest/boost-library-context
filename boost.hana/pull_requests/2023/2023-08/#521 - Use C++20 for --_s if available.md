# #521 Use C++20 for ""_s if available [Open]

> Username: ecatmur  
> Created at: 2023-08-08 05:39:21 UTC  
> Updated at: 2023-08-08 23:47:56 UTC  
> Url: https://github.com/boostorg/hana/pull/521  

In C++20, we are supposed to use CTAD NTTPs to implement string UDL templates.  
  
Continue to use BOOST_HANA_CONFIG_ENABLE_STRING_UDL to indicate that the string UDL template is available, but enable that macro by default if C++20 is detected.  
  
Compiler support detection is a bit involved, unfortunately, since there isn't a specific feature test macro for it and it appears to have been added in point releases for some compilers.

---
