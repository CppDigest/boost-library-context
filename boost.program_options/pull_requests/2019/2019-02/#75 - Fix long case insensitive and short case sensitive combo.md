# #75 Fix long case insensitive and short case sensitive combo. [Open]

> Username: Landberg  
> Created at: 2019-02-07 09:44:35 UTC  
> Updated at: 2019-02-07 19:27:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/75  

The local_option update for the short_name match was removed in v1.68.0, but it is needed to allow for case insensitive long names combined with case sensitive short names.  See #74 for more info about the problem.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-02-07 12:05:02 UTC  
> Updated_at: 2019-02-07 19:27:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/75#issuecomment-461392783  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/75?src=pr&el=h1) Report  
> Merging [#75](https://codecov.io/gh/boostorg/program_options/pull/75?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/d95d31684832075fda04c9fc916f8ec875552763?src=pr&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `0%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/75/graphs/tree.svg?width=650&token=UMdM0EgHK3&height=150&src=pr)](https://codecov.io/gh/boostorg/program_options/pull/75?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #75      +/-   ##  
===========================================  
+ Coverage    49.89%   49.92%   +0.03%       
===========================================  
  Files           23       23                
  Lines         1385     1386       +1       
  Branches       707      708       +1       
===========================================  
+ Hits           691      692       +1       
  Misses         110      110                
  Partials       584      584  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/75?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/options\_description.cpp](https://codecov.io/gh/boostorg/program_options/pull/75/diff?src=pr&el=tree#diff-c3JjL29wdGlvbnNfZGVzY3JpcHRpb24uY3Bw) | `48.46% <0%> (-0.19%)` | :arrow_down: |  
| [...de/boost/program\_options/detail/value\_semantic.hpp](https://codecov.io/gh/boostorg/program_options/pull/75/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL3ZhbHVlX3NlbWFudGljLmhwcA==) | `43.75% <0%> (+1.56%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/75?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/75?src=pr&el=footer). Last update [d95d316...3ee744c](https://codecov.io/gh/boostorg/program_options/pull/75?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
