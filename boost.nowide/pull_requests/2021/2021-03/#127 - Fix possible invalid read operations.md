# #127 Fix possible invalid read operations [Merged]

> Username: Flamefire  
> Created at: 2021-03-20 13:04:27 UTC  
> Updated at: 2021-03-21 15:15:41 UTC  
> Merged at: 2021-03-21 15:15:38 UTC  
> Closed at: 2021-03-21 15:15:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/127  

- Read text files char-by-char. Reduces performance but is correct even when using seek (also indirectly via tellg), fixes #126   
- Reset stream pointers in close to avoid a use-after-free  
- Fix swap for single-char states

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-03-21 14:43:21 UTC  
> Url: https://github.com/boostorg/nowide/pull/127#issuecomment-803594689  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/127?src=pr&el=h1) Report  
> Merging [#127](https://codecov.io/gh/boostorg/nowide/pull/127?src=pr&el=desc) (ca273d5) into [develop](https://codecov.io/gh/boostorg/nowide/commit/0653d36da32663c44cb25a209450bc4ad24eb080?el=desc) (0653d36) will **increase** coverage by `0.48%`.  
> The diff coverage is `96.25%`.  
  
> :exclamation: Current head ca273d5 differs from pull request most recent head 87e3f5d. Consider uploading reports for the commit 87e3f5d to get more accurate results  
```diff  
@@             Coverage Diff             @@  
##           develop     #127      +/-   ##  
===========================================  
+ Coverage    89.69%   90.17%   +0.48%       
===========================================  
  Files           28       28                
  Lines         2077     2138      +61       
  Branches       183      185       +2       
===========================================  
+ Hits          1863     1928      +65       
+ Misses         208      204       -4       
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/127?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/127/diff?src=pr&el=tree#diff-c3JjL2ZpbGVidWYuY3Bw) | `90.90% <ø> (ø)` | |  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/127/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `81.57% <75.00%> (+1.93%)` | :arrow_up: |  
| [test/test\_fstream.cpp](https://codecov.io/gh/boostorg/nowide/pull/127/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2ZzdHJlYW0uY3Bw) | `99.15% <100.00%> (+0.16%)` | :arrow_up: |

---
