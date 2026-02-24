# #236 Use BOOST_FALLTHROUGH in place of comments. [Open]

> Username: vslashg  
> Created at: 2023-12-01 20:20:41 UTC  
> Updated at: 2023-12-01 20:20:41 UTC  
> Url: https://github.com/boostorg/date_time/pull/236  

Some static analyzers complain about unannotated case fallthrough; using BOOST_FALLTHROUGH from config.hpp silences these and serves the same documentation purpose.

---
