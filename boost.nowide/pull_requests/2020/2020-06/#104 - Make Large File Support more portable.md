# #104 Make Large File Support more portable [Merged]

> Username: Flamefire  
> Created at: 2020-06-09 09:49:09 UTC  
> Updated at: 2020-06-12 13:53:47 UTC  
> Merged at: 2020-06-12 13:53:43 UTC  
> Closed at: 2020-06-12 13:53:44 UTC  
> Url: https://github.com/boostorg/nowide/pull/104  

The macro based solution has the downside of cluttering the global  
namespace and requires duplication in both config headers.  
Additionally ftello64 might not be available on some system where the  
define check succeeds.  
  
This solution uses a cpp file to wrap the functions adding enhanced  
exists checks and additionally checking for overflow returning -1  
(error) in that case as mandated by the standard for the filebuf  
functions.  
  
Finally a configure check determines general availability of LFS and if  
it is not available the standard fseek/ftell functions are used.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-09 18:52:18 UTC  
> Updated_at: 2020-06-12 13:42:51 UTC  
> Url: https://github.com/boostorg/nowide/pull/104#issuecomment-641505073  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/104?src=pr&el=h1) Report  
> Merging [#104](https://codecov.io/gh/boostorg/nowide/pull/104?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/517e13bd3fdd93db8ed550bd0cd2ae109aaaf990&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `93.54%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #104      +/-   ##  
===========================================  
+ Coverage    90.13%   90.17%   +0.03%       
===========================================  
  Files           23       24       +1       
  Lines         2504     2524      +20       
  Branches       183      185       +2       
===========================================  
+ Hits          2257     2276      +19       
- Misses         241      242       +1       
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/104?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/104/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `82.28% <91.66%> (+0.06%)` | :arrow_up: |  
| [src/filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/104/diff?src=pr&el=tree#diff-c3JjL2ZpbGVidWYuY3Bw) | `94.73% <94.73%> (ø)` | |

---

## Comment 2

> Username: Flamefire  
> Created_at: 2020-06-10 07:07:50 UTC  
> Url: https://github.com/boostorg/nowide/pull/104#issuecomment-641777772  

@davidwed Would you mind checking this if I missed anything and give it a try to see if it works for you? I tried very hard to make it compatible with any obscure platform where Boost might be used at hence the slightly advanced code.

---

## Comment 3

> Username: davidwed  
> Created_at: 2020-06-12 11:54:06 UTC  
> Url: https://github.com/boostorg/nowide/pull/104#issuecomment-643230784  

Looks great!

---
