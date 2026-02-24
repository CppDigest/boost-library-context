# #226 Make ICU implementation of to_title threadsafe [Merged]

> Username: Flamefire  
> Created at: 2024-04-03 08:08:04 UTC  
> Updated at: 2024-04-03 11:07:56 UTC  
> Merged at: 2024-04-03 11:07:50 UTC  
> Closed at: 2024-04-03 11:07:50 UTC  
> Url: https://github.com/boostorg/locale/pull/226  

`ucasemap_utf8ToTitle` uses a non-const casemap hence reusing the existing one isn't threadsafe.  
Add constraints to `raii_casemap::convert` on the constness of `Func` and create a temporary casemap.  
  
Fixes #225

---
