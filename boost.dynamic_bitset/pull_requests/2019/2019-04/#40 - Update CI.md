# #40 Update CI [Merged]

> Username: jeking3  
> Created at: 2019-04-22 16:30:47 UTC  
> Updated at: 2019-04-23 14:40:40 UTC  
> Merged at: 2019-04-23 14:40:35 UTC  
> Closed at: 2019-04-23 14:40:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/40  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-23 10:49:51 UTC  
> Updated_at: 2019-04-23 14:33:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/40#issuecomment-485753166  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40?src=pr&el=h1) Report  
> Merging [#40](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/1b8b128f29a21ef707c919f4681fcceeef44a5e9?src=pr&el=desc) will **increase** coverage by `1.73%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #40      +/-   ##  
===========================================  
+ Coverage    77.15%   78.88%   +1.73%       
===========================================  
  Files            5        5                
  Lines          604      611       +7       
  Branches       211      203       -8       
===========================================  
+ Hits           466      482      +16       
  Misses          24       24                
+ Partials       114      105       -9  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `77.93% <0%> (+1.69%)` | :arrow_up: |  
| [...ude/boost/dynamic\_bitset/detail/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9kZXRhaWwvZHluYW1pY19iaXRzZXQuaHBw) | `96.77% <0%> (+3.22%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40?src=pr&el=footer). Last update [1b8b128...9d2e3fb](https://codecov.io/gh/boostorg/dynamic_bitset/pull/40?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-04-23 14:39:07 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/40#issuecomment-485831608  

I opened #40 to track the gcc 4.8 regression in xenial.  It is (for now) an allowed failure so we can make progress.

---
