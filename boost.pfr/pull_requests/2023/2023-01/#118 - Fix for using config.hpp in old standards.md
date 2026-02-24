# #118 Fix for using config.hpp in old standards [Merged]

> Username: denzor200  
> Created at: 2023-01-06 19:50:37 UTC  
> Updated at: 2023-01-07 15:37:50 UTC  
> Merged at: 2023-01-07 15:37:50 UTC  
> Closed at: 2023-01-07 15:37:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/118  

Additionaly for https://github.com/boostorg/pfr/pull/86

---

## Comment 1

> Username: coveralls  
> Created_at: 2023-01-06 20:12:35 UTC  
> Updated_at: 2023-01-07 11:16:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/118#issuecomment-1374078105  

## Pull Request Test Coverage Report for [Build 3861773297](https://coveralls.io/builds/55728989)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/55728989/badge)](https://coveralls.io/builds/55728989) |  
| :-- | --: |  
| Change from base [Build 3807146163](https://coveralls.io/builds/55534705): |  0.0% |  
| Covered Lines: | 337 |  
| Relevant Lines: | 337 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Review 2 [Commented]

> Username: denzor200  
> Created_at: 2023-01-06 21:04:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/118#pullrequestreview-1239408632  

📁 include/boost/pfr/config.hpp

```diff
   9 | #pragma once
  10 | 
  11 |+ #if __cplusplus >= 201402L || (defined(_MSC_VER) && defined(_MSVC_LANG) && _MSC_VER > 1900)
```

> Username: denzor200  
> Created_at: 2023-01-06 21:04:25 UTC  
> Updated_at: 2023-01-06 21:04:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/118#discussion_r1063799352  

`(defined(_MSC_VER) && defined(_MSVC_LANG) && _MSC_VER > 1900)`  
this won't tested, add old MSVC to CI


---
