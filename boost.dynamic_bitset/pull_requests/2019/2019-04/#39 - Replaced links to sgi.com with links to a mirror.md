# #39 Replaced links to sgi.com with links to a mirror. [Merged]

> Username: tinko92  
> Created at: 2019-04-20 22:13:04 UTC  
> Updated at: 2019-04-23 12:25:35 UTC  
> Merged at: 2019-04-22 16:20:40 UTC  
> Closed at: 2019-04-22 16:20:40 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/39  

Hi, I've noticed that http://www.sgi.com/tech/stl/ is gone (apparently since about a year ago), so all those links to sgi.com should probably be replaced. There is a mirror at http://www.martinbroadhurst.com/stl/ , though. This PR replaces the links with links to the mirror.

---

## Review 1 [Changes requested]

> Username: jeking3  
> Created_at: 2019-04-22 12:47:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/39#pullrequestreview-229049882  

Please change to http://www.rrsd.com/software_development/stl/stl/index.html which is the place most of the boost documentation has been changed to point to.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-04-22 15:13:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/39#issuecomment-485445692  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39?src=pr&el=h1) Report  
> Merging [#39](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/91625cece1249396fc3766feffbfe11a3e0026c6?src=pr&el=desc) will **increase** coverage by `1.73%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #39      +/-   ##  
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
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `77.93% <0%> (+1.69%)` | :arrow_up: |  
| [...ude/boost/dynamic\_bitset/detail/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9kZXRhaWwvZHluYW1pY19iaXRzZXQuaHBw) | `96.77% <0%> (+3.22%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39?src=pr&el=footer). Last update [91625ce...4c9afe1](https://codecov.io/gh/boostorg/dynamic_bitset/pull/39?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: tinko92  
> Created_at: 2019-04-22 15:15:44 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/39#issuecomment-485446376  

Thanks for pointing out the preferred location, I amended my commit.

---

## Comment 4

> Username: glenfe  
> Created_at: 2019-04-23 12:25:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/39#issuecomment-485780557  

> Please change to http://www.rrsd.com/software_development/stl/stl/index.html which is the place most of the boost documentation has been changed to point to.  
  
They should be changed to https://www.boost.org/sgi/stl/ (not rrsd.com).

---
