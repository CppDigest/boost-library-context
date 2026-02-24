# #50 Avoid redefinition of BOOST_JSON_SOURCE macro [Closed]

> Username: mloskot  
> Created at: 2020-03-07 22:56:32 UTC  
> Updated at: 2020-03-08 01:01:10 UTC  
> Closed at: 2020-03-08 00:08:08 UTC  
> Url: https://github.com/boostorg/json/pull/50  

```  
../../boost/json/src.hpp:20: warning: "BOOST_JSON_SOURCE" redefined  
   20 | #define BOOST_JSON_SOURCE  
      |  
```  
  
due to  
  
https://github.com/vinniefalco/json/blob/37d0a09242bf0d116c0cd69415034ff93fd9cc02/build/Jamfile#L14  
  
Similarly, may users do  
  
```cpp  
#define BOOST_JSON_SOURCE  
#include <boost/json/src.hpp>  
```  
?

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-07 23:48:06 UTC  
> Updated_at: 2020-03-08 00:07:19 UTC  
> Url: https://github.com/boostorg/json/pull/50#issuecomment-596149270  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/50?src=pr&el=h1) Report  
> Merging [#50](https://codecov.io/gh/vinniefalco/json/pull/50?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/37d0a09242bf0d116c0cd69415034ff93fd9cc02?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/50/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/50?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #50   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4854     4854             
========================================  
  Hits          4810     4810             
  Misses          44       44  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/50?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/50?src=pr&el=footer). Last update [37d0a09...6cad113](https://codecov.io/gh/vinniefalco/json/pull/50?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-03-08 00:05:55 UTC  
> Url: https://github.com/boostorg/json/pull/50#issuecomment-596150496  

Thanks!

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-03-08 00:08:08 UTC  
> Url: https://github.com/boostorg/json/pull/50#issuecomment-596150661  

Thanks!

---
