# #112 Fix LFS detection (e.g. on Android) [Merged]

> Username: Flamefire  
> Created at: 2020-07-21 17:40:41 UTC  
> Updated at: 2020-07-22 14:05:15 UTC  
> Merged at: 2020-07-22 14:05:12 UTC  
> Closed at: 2020-07-22 14:05:12 UTC  
> Url: https://github.com/boostorg/nowide/pull/112  

(At least) the Android NDKs stdio.h does define fseeko but not if __USE_FILE_OFFSET64  
is defined and the API level is below 24.  
As the usage of this macro was inconsistent between the check and the  
use LFS could be wrongly detected leading to a compile failure later.  
  
Fixes #111

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-21 18:57:52 UTC  
> Updated_at: 2020-07-21 19:30:14 UTC  
> Url: https://github.com/boostorg/nowide/pull/112#issuecomment-662046702  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/112?src=pr&el=h1) Report  
> Merging [#112](https://codecov.io/gh/boostorg/nowide/pull/112?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/d0806d171da4748e17f36ec049d72c1fcdf8cabb&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #112   +/-   ##  
========================================  
  Coverage    90.31%   90.31%             
========================================  
  Files           27       27             
  Lines         2561     2561             
  Branches       185      185             
========================================  
  Hits          2313     2313             
  Misses         242      242             
  Partials         6        6             
```

---
