# #64 Fix catch exception by value warning [Merged]

> Username: mborland  
> Created at: 2022-07-09 23:26:08 UTC  
> Updated at: 2022-07-11 20:39:39 UTC  
> Merged at: 2022-07-11 20:39:39 UTC  
> Closed at: 2022-07-11 20:39:39 UTC  
> Url: https://github.com/boostorg/chrono/pull/64  

Fixes: -Werror=catch-value

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-07-11 14:59:14 UTC  
> Url: https://github.com/boostorg/chrono/pull/64#issuecomment-1180519972  

Should catch by `const &` as more idiomatic.

---
