# #7 Fix MSVC C4668 warnings [Merged]

> Username: pkolbus  
> Created at: 2018-03-27 03:44:35 UTC  
> Updated at: 2018-09-27 04:00:34 UTC  
> Merged at: 2018-09-27 04:00:34 UTC  
> Closed at: 2018-09-27 04:00:34 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/7  

This PR fixes a couple of MSVC C4668 warnings:  
  
...\boost\throw_exception.hpp(3) : warning C4668: '__GNUC__' is not defined as a preprocessor macro, replacing with '0' for '#if/#elif'            
...\boost\throw_exception.hpp(3) : warning C4668: '__GNUC_MINOR__' is not defined as a preprocessor macro, replacing with '0' for '#if/#elif'      
...\boost\exception\exception.hpp(8) : warning C4668: '__GNUC__' is not defined as a preprocessor macro, replacing with '0' for '#if/#elif'        
...\boost\exception\exception.hpp(8) : warning C4668: '__GNUC_MINOR__' is not defined as a preprocessor macro, replacing with '0' for '#if/#elif'

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-09-26 13:03:54 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/7#issuecomment-424707038  

Please rebase against the current develop.

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-09-27 03:29:53 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/7#issuecomment-424947059  

Never mind.

---
