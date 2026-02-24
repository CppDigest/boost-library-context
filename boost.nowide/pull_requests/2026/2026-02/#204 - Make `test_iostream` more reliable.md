# #204 Make `test_iostream` more reliable [Merged]

> Username: Flamefire  
> Created at: 2026-02-22 16:27:25 UTC  
> Updated at: 2026-02-22 16:55:29 UTC  
> Merged at: 2026-02-22 16:55:26 UTC  
> Closed at: 2026-02-22 16:55:26 UTC  
> Url: https://github.com/boostorg/nowide/pull/204  

On CI using MinGW mocking the stdin buffer sometimes fails.  
It should read:  
  
> Hello std in  
> ä - ö - ü - м - ν  
  
However sometimes the 2nd line is returned instead of the first or the first is repeated.  
This only reproduces on CI, so detect and ignore those known cases

---
