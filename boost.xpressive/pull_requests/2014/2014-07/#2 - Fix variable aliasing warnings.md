# #2 Fix variable aliasing warnings [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-05 15:51:54 UTC  
> Updated at: 2014-07-07 04:37:24 UTC  
> Merged at: 2014-07-07 02:55:45 UTC  
> Closed at: 2014-07-07 02:55:45 UTC  
> Url: https://github.com/boostorg/xpressive/pull/2  

MSVC 14 has new variable shadowing warnings, which are on by default and spam the Boost regression test output:  
"warning C4456: declaration of '...' hides previous local declaration".  
This renames a few variables to make MSVC happy.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2014-07-07 02:55:43 UTC  
> Url: https://github.com/boostorg/xpressive/pull/2#issuecomment-48137001  

Thanks!

---
