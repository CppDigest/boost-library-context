# #558 Narrow Proto and Phoenix inclusion [Merged]

> Username: Kojoley  
> Created at: 2019-11-29 00:21:20 UTC  
> Updated at: 2019-11-29 10:37:27 UTC  
> Merged at: 2019-11-29 10:37:20 UTC  
> Closed at: 2019-11-29 10:37:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/558  

Include needed parts at place where they are needed instead of including the  
whole Proto library and significant parts of Phoenix in a few places.  
  
Parsing time reported by Clang 9 with default (C++14) std:  
  
was |now |gain|include  
----|----|----|----------  
4.45|4.22|5.2%|qi  
4.52|4.23|7.5%|karma  
4.37|3.99|9.7%|lex  
3.75|3.47|7.5%|qi_numeric  
  
There were phoenix/limits.hpp inclusion before every Proto inclusion to override  
Proto limits with Phoenix limits values. It has no value in C++11+ world, and  
poisons code too much as the number of places where Proto headers now included  
increased, so I did not replicate it.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-11-29 02:30:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/558#issuecomment-559639187  

Wonderful!

---
