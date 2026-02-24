# #141 - Construction of `result<T>` with an `error_code` from a user-defined category isn't constexpr even under C++20 [Closed]

> Username: pdimov  
> Created at: 2026-02-09 17:59:51 UTC  
> Updated at: 2026-02-20 03:40:23 UTC  
> Closed at: 2026-02-19 07:43:18 UTC  
> Url: https://github.com/boostorg/system/issues/141  

https://github.com/boostorg/url/issues/890#issuecomment-3868153507  
https://godbolt.org/z/38nTdvzEj

---

## Comment 1

> Username: pdimov  
> Created at: 2026-02-16 02:23:31 UTC  
> Url: https://github.com/boostorg/system/issues/141#issuecomment-3906120062  

Should be fixed on develop.  
  
@alandefreitas

---

## Comment 2

> Username: alandefreitas  
> Created at: 2026-02-17 22:16:27 UTC  
> Url: https://github.com/boostorg/system/issues/141#issuecomment-3917356611  

Amazing. Already integrated into https://github.com/boostorg/url/pull/976

---

## Comment 3

> Username: alandefreitas  
> Created at: 2026-02-20 02:46:07 UTC  
> Url: https://github.com/boostorg/system/issues/141#issuecomment-3931289313  

Hi. I also merged the changes to develop in boost.url but don't know when to merge to master. Are your changes in master yet?

---

## Comment 4

> Username: pdimov  
> Created at: 2026-02-20 03:40:23 UTC  
> Url: https://github.com/boostorg/system/issues/141#issuecomment-3931423241  

They are now.
