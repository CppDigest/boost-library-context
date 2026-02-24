# #471 Update cxxstd values for GHA msvc jobs [Merged]

> Username: pdimov  
> Created at: 2023-01-24 01:17:46 UTC  
> Updated at: 2023-11-01 11:28:47 UTC  
> Merged at: 2023-11-01 11:28:46 UTC  
> Closed at: 2023-11-01 11:28:46 UTC  
> Url: https://github.com/boostorg/config/pull/471  

msvc-14.0 doesn't have `/std:c++17`, only `/std:c++latest`, so the `cxxstd=17` job did nothing.  
  
While here I also added 20 to msvc-14.2 and msvc-14.3, because they support that now.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-11-01 09:48:06 UTC  
> Url: https://github.com/boostorg/config/pull/471#issuecomment-1788682587  

Ping?

---
