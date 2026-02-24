# #35 Disable hardware supported popcount on MSVC [Merged]

> Username: jeking3  
> Created at: 2019-02-21 16:38:04 UTC  
> Updated at: 2019-02-22 13:36:16 UTC  
> Merged at: 2019-02-22 13:36:13 UTC  
> Closed at: 2019-02-22 13:36:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/35  

- the new implementation failed to check for hardware support  
- the behavior was undefined on platforms without SSE4  
  
This fixes #33   
  
I will open another issue regarding proper implementation for MSVC.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2019-02-21 16:55:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/35#pullrequestreview-206422893  

📁 include/boost/dynamic_bitset/detail/dynamic_bitset.hpp

```diff
 148 |+ 		 per https://github.com/boostorg/dynamic_bitset/issues/33
 149 |+ 		 this code does not support older cpus properly...
 150 |+ 		 it needs to check for cpuid support
```

> Username: pdimov  
> Created_at: 2019-02-21 16:55:57 UTC  
> Updated_at: 2019-02-21 17:04:07 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/35#discussion_r259020992  

Tabs?

> Username: jeking3  
> Created_at: 2019-02-21 17:00:13 UTC  
> Updated_at: 2019-02-21 17:04:07 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/35#discussion_r259022916  

Ick.  My windows editor was MSVC... see what it did?  I bet there's a GitHub PR hook that checks for stuff like that... perhaps "mergeable" can do that...


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-02-21 20:02:19 UTC  
> Updated_at: 2019-02-21 20:02:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/35#issuecomment-466145385  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35?src=pr&el=h1) Report  
> Merging [#35](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/a580c496faa497335d589f8426b48deaccc9215a?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #35   +/-   ##  
========================================  
  Coverage    77.15%   77.15%             
========================================  
  Files            5        5             
  Lines          604      604             
  Branches       211      211             
========================================  
  Hits           466      466             
  Misses          24       24             
  Partials       114      114  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/dynamic\_bitset/detail/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9kZXRhaWwvZHluYW1pY19iaXRzZXQuaHBw) | `93.54% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35?src=pr&el=footer). Last update [a580c49...8b87ff2](https://codecov.io/gh/boostorg/dynamic_bitset/pull/35?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
