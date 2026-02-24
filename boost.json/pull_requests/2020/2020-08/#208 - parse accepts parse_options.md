# #208 parse accepts parse_options [Closed]

> Username: sdkrystian  
> Created at: 2020-08-25 15:27:50 UTC  
> Updated at: 2020-08-27 02:41:25 UTC  
> Closed at: 2020-08-27 02:41:25 UTC  
> Url: https://github.com/boostorg/json/pull/208  

fix #206

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-25 15:42:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/208#pullrequestreview-474611176  

📁 include/boost/json/parse.hpp

```diff
  51 |     the default memory resource is used.
  52 |+ 
  53 |+      @param opt The options for the parser.
```

> Username: vinniefalco  
> Created_at: 2020-08-25 15:42:38 UTC  
> Updated_at: 2020-08-25 15:46:55 UTC  
> Url: https://github.com/boostorg/json/pull/208#discussion_r476548536  

add  
```  
@see ref parse_options  
```


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-25 15:42:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/208#pullrequestreview-474611416  

📁 include/boost/json/parse.hpp

```diff
  87 |     the default memory resource is used.
  88 | 
  89 |+      @param opt The options for the parser.
```

> Username: vinniefalco  
> Created_at: 2020-08-25 15:42:53 UTC  
> Updated_at: 2020-08-25 15:46:55 UTC  
> Url: https://github.com/boostorg/json/pull/208#discussion_r476548708  

spacing


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-08-26 06:55:38 UTC  
> Updated_at: 2020-08-26 12:13:46 UTC  
> Url: https://github.com/boostorg/json/pull/208#issuecomment-680696384  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/208?src=pr&el=h1) Report  
> Merging [#208](https://codecov.io/gh/CPPAlliance/json/pull/208?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/523332ae81a01ccb26a3df1c087f9b58fcdde3de?el=desc) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/208/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/208?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #208   +/-   ##  
========================================  
  Coverage    97.93%   97.93%             
========================================  
  Files           65       65             
  Lines         5654     5654             
========================================  
  Hits          5537     5537             
  Misses         117      117             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/208?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/parse.ipp](https://codecov.io/gh/CPPAlliance/json/pull/208/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2UuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/208?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/208?src=pr&el=footer). Last update [523332a...80bd692](https://codecov.io/gh/CPPAlliance/json/pull/208?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
