# #236 Add test for formatting large (u)int64 numbers [Closed]

> Username: Flamefire  
> Created at: 2024-08-08 08:11:07 UTC  
> Updated at: 2024-12-21 10:40:45 UTC  
> Closed at: 2024-12-21 10:40:44 UTC  
> Url: https://github.com/boostorg/locale/pull/236  

As reported in #235 formatting the first number which doesn't fit into int64_t anymore fails to add the thousands separators. I.e.:  
`9223372036854775807` -> `9,223,372,036,854,775,807`     
`9223372036854775808` -> `9223372036854775808`  
  
Add a test reproducing that that for all backends.

---
