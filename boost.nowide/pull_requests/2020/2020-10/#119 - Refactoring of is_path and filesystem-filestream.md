# #119 Refactoring of is_path and filesystem/filestream [Merged]

> Username: Flamefire  
> Created at: 2020-10-25 12:50:50 UTC  
> Updated at: 2020-10-25 19:17:58 UTC  
> Merged at: 2020-10-25 19:17:55 UTC  
> Closed at: 2020-10-25 19:17:55 UTC  
> Url: https://github.com/boostorg/nowide/pull/119  

Simplify some code and add test for the trait

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-10-25 14:00:16 UTC  
> Updated_at: 2020-10-25 19:14:42 UTC  
> Url: https://github.com/boostorg/nowide/pull/119#issuecomment-716152960  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/119?src=pr&el=h1) Report  
> Merging [#119](https://codecov.io/gh/boostorg/nowide/pull/119?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/8d86904d88d77f33d07077933f9c19da64583c8b?el=desc) will **increase** coverage by `3.09%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #119      +/-   ##  
===========================================  
+ Coverage    86.26%   89.35%   +3.09%       
===========================================  
  Files           41       28      -13       
  Lines         3021     2057     -964       
  Branches       185      185                
===========================================  
- Hits          2606     1838     -768       
+ Misses         308      213      -95       
+ Partials       107        6     -101       
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/119?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/fstream.hpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZnN0cmVhbS5ocHA=) | `97.56% <100.00%> (+18.68%)` | :arrow_up: |  
| [test/test\_codecvt.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `95.16% <100.00%> (ø)` | |  
| [test/test\_traits.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3RyYWl0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [src/cstdlib.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-c3JjL2NzdGRsaWIuY3Bw) | `89.58% <0.00%> (-10.42%)` | :arrow_down: |  
| [include/boost/nowide/args.hpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvYXJncy5ocHA=) | `95.91% <0.00%> (-4.09%)` | :arrow_down: |  
| [C:/projects/nowide-fr98b/test/test\_fs.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-QzovcHJvamVjdHMvbm93aWRlLWZyOThiL3Rlc3QvdGVzdF9mcy5jcHA=) | | |  
| [C:/projects/nowide-fr98b/test/test\_env.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-QzovcHJvamVjdHMvbm93aWRlLWZyOThiL3Rlc3QvdGVzdF9lbnYuY3Bw) | | |  
| [C:/projects/nowide-fr98b/test/test\_system.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-QzovcHJvamVjdHMvbm93aWRlLWZyOThiL3Rlc3QvdGVzdF9zeXN0ZW0uY3Bw) | | |  
| [C:/projects/nowide-fr98b/src/filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-QzovcHJvamVjdHMvbm93aWRlLWZyOThiL3NyYy9maWxlYnVmLmNwcA==) | | |  
| [C:/projects/nowide-fr98b/src/stat.cpp](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree#diff-QzovcHJvamVjdHMvbm93aWRlLWZyOThiL3NyYy9zdGF0LmNwcA==) | | |  
| ... and [48 more](https://codecov.io/gh/boostorg/nowide/pull/119/diff?src=pr&el=tree-more) | |

---
