# #308 Use <version> to determine standard library [Merged]

> Username: Kojoley  
> Created at: 2019-12-03 16:36:10 UTC  
> Updated at: 2020-01-24 18:07:24 UTC  
> Merged at: 2020-01-24 18:07:24 UTC  
> Closed at: 2020-01-24 18:07:24 UTC  
> Url: https://github.com/boostorg/config/pull/308  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-12-03 18:34:32 UTC  
> Url: https://github.com/boostorg/config/pull/308#issuecomment-561298532  

I'm not necessarily against this (as long as it works OK), but note that <cstddef> is a pretty tiny header already, so I doubt this change will have much impact?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-12-03 18:50:50 UTC  
> Url: https://github.com/boostorg/config/pull/308#issuecomment-561304754  

Compile time impact is very tiny (clang+msvc stdlib is 0.012 vs 0.007), and IIUC `<version>` should be included with any other standard library header. On the other hand, including less things from standard library will force self-sustainability of headers that include `boost/config.hpp` but forgets to include `cstddef`.

---
