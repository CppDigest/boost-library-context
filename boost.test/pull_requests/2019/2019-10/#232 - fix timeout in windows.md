# #232 fix timeout in windows [Closed]

> Username: mmitti  
> Created at: 2019-10-24 09:57:00 UTC  
> Updated at: 2019-11-03 10:15:24 UTC  
> Closed at: 2019-11-03 10:13:53 UTC  
> Url: https://github.com/boostorg/test/pull/232  

Visual studio 2019 + Boost 1.71.0 + Windows 10 64bit  
  
I use boost test with timeout(240) like below, it will timeout error.  
  
```cpp  
BOOST_AUTO_TEST_CASE(TEST, *boost::unit_test::timeout(240)) {  
    BOOST_TEST_INFO(1);  
}  
```  
  
DueTime is 64bit, but `static_cast<signed long int>(p_timeout) * 10` will 32bit and timeout value will overflow.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2019-10-31 13:27:21 UTC  
> Url: https://github.com/boostorg/test/pull/232#issuecomment-548374061  

Replaced with `LONGLONG` instead. Currently building in the branch `topic/PR-232-fix-timeout-in-windows`. Thanks!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-11-01 02:39:49 UTC  
> Url: https://github.com/boostorg/test/pull/232#issuecomment-548643978  

# [Codecov](https://codecov.io/gh/boostorg/test/pull/232?src=pr&el=h1) Report  
> Merging [#232](https://codecov.io/gh/boostorg/test/pull/232?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/test/commit/a1bafb22eb38575f3594b7c9a9de764ad7f090f0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/test/pull/232/graphs/tree.svg?width=650&token=CXdgaxTES0&height=150&src=pr)](https://codecov.io/gh/boostorg/test/pull/232?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #232   +/-   ##  
========================================  
  Coverage    55.78%   55.78%             
========================================  
  Files          112      112             
  Lines         5890     5890             
  Branches      2380     2380             
========================================  
  Hits          3286     3286             
  Misses         778      778             
  Partials      1826     1826  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/test/pull/232?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/test/impl/execution\_monitor.ipp](https://codecov.io/gh/boostorg/test/pull/232/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC90ZXN0L2ltcGwvZXhlY3V0aW9uX21vbml0b3IuaXBw) | `38.02% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/test/pull/232?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/test/pull/232?src=pr&el=footer). Last update [a1bafb2...d26783b](https://codecov.io/gh/boostorg/test/pull/232?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2019-11-03 10:15:23 UTC  
> Url: https://github.com/boostorg/test/pull/232#issuecomment-549122239  

Merged to master, thanks!

---
