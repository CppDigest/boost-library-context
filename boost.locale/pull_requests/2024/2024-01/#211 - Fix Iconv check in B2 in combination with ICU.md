# #211 Fix Iconv check in B2 in combination with ICU [Merged]

> Username: Flamefire  
> Created at: 2024-01-23 18:10:26 UTC  
> Updated at: 2024-01-24 18:07:43 UTC  
> Merged at: 2024-01-24 18:07:40 UTC  
> Closed at: 2024-01-24 18:07:40 UTC  
> Url: https://github.com/boostorg/locale/pull/211  

Including ICU might bring another Iconv in (e.g. on FreeBSD) which has different symbols.  
So check for Iconv after adding the ICU options

---
