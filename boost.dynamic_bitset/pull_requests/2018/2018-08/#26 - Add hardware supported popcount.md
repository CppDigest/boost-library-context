# #26 Add hardware supported popcount [Merged]

> Username: Izaron  
> Created at: 2018-08-23 13:51:29 UTC  
> Updated at: 2018-09-02 01:56:59 UTC  
> Merged at: 2018-09-02 01:56:59 UTC  
> Closed at: 2018-09-02 01:56:59 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/26  

Related issue - https://github.com/boostorg/dynamic_bitset/issues/25

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-08-23 14:47:51 UTC  
> Updated_at: 2018-09-01 23:46:43 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/26#issuecomment-415445377  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26?src=pr&el=h1) Report  
> Merging [#26](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/b944aa300867faff61befb9f6de38b360f01aecf?src=pr&el=desc) will **increase** coverage by `0.44%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #26      +/-   ##  
===========================================  
+ Coverage    75.41%   75.86%   +0.44%       
===========================================  
  Files            5        5                
  Lines          541      551      +10       
  Branches       198      199       +1       
===========================================  
+ Hits           408      418      +10       
  Misses          23       23                
  Partials       110      110  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `74.75% <ø> (ø)` | :arrow_up: |  
| [include/boost/detail/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kZXRhaWwvZHluYW1pY19iaXRzZXQuaHBw) | `93.54% <100%> (+3.07%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26?src=pr&el=footer). Last update [b944aa3...a87cbee](https://codecov.io/gh/boostorg/dynamic_bitset/pull/26?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Approved]

> Username: jeking3  
> Created_at: 2018-08-29 02:58:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/26#pullrequestreview-150386695  

Would probably run this in the debugger to satisfy myself that it's actually making it into the template specializations you added... but it does pass CI which is a good thing.  It's too bad there's no standard for this...

---

## Comment 3

> Username: Izaron  
> Created_at: 2018-08-30 18:09:32 UTC  
> Updated_at: 2018-08-30 18:11:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/26#issuecomment-417415116  

@jeking3 Thank you! Actually we started to discuss some "standardization" of intrinsics/"smart"/fast bit algorithms and their problems in the boost mailing list - the thread is [here](https://lists.boost.org/Archives/boost/2018/08/242926.php). It could be part of an existing library, probably Boost.Integer.  
  
The main problem for me is testing the intrinsics code - though there are only two major intrinsic versions (MSVC/Gcc or Clang), it apparently requires a heap of possible environments of different systems, compilers, their versions... I didn't manage to get Appveyor to work, but you may look at [Travis](https://travis-ci.org/Izaron/bit_algo) outcome - click on a job and scroll down to see differences between naive algos and smart ones on a handcrafted benchmark.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-08-31 19:09:30 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/26#issuecomment-417762840  

Curious if you have any benchmarks showing the performance improvement of the changes?

---

## Comment 5

> Username: Izaron  
> Created_at: 2018-09-01 22:48:52 UTC  
> Updated_at: 2018-09-01 22:59:17 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/26#issuecomment-417892635  

Yes I do, I wrote a benchmark using [this library](https://github.com/google/benchmark), there is [source code](https://gist.github.com/Izaron/f756cb6a85e42a13480b688825766de8), [old performance](https://gist.github.com/Izaron/5617bfd4219316d96819bdd4ead51323), [new](https://gist.github.com/Izaron/32760494ba66f269732f8c524e91d6aa). It does nothing on small types, slightly speeds up int and rushes forward on longs.  
I honestly didn't test the performance on Windows, I just guess that it should show the same outcome.  
(P.S. Changed the PR a bit - just added some copyright line about me...)

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-09-02 01:55:24 UTC  
> Updated_at: 2018-09-02 01:56:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/26#issuecomment-417899299  

Nice, so performance improvement on 64-bit is 2.375x (3.8 / 1.6).  Thanks.

---
