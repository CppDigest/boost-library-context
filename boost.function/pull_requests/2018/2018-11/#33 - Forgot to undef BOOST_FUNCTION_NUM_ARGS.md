# #33 Forgot to undef BOOST_FUNCTION_NUM_ARGS [Merged]

> Username: Kojoley  
> Created at: 2018-11-01 01:07:06 UTC  
> Updated at: 2018-11-01 12:40:39 UTC  
> Merged at: 2018-11-01 12:40:01 UTC  
> Closed at: 2018-11-01 12:40:01 UTC  
> Url: https://github.com/boostorg/function/pull/33  

Fixes macro redefinition warning from:  
```cpp  
#include <boost/function.hpp>  
#include <boost/function/function0.hpp>  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-01 04:48:47 UTC  
> Updated_at: 2018-11-01 04:49:54 UTC  
> Url: https://github.com/boostorg/function/pull/33#issuecomment-434929674  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/33?src=pr&el=h1) Report  
> Merging [#33](https://codecov.io/gh/boostorg/function/pull/33?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/function/commit/f22fa49e078b1bbef31e2a472135e17847ab578a?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/33/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/33?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #33   +/-   ##  
========================================  
  Coverage    54.24%   54.24%             
========================================  
  Files            4        4             
  Lines          389      389             
  Branches        98       98             
========================================  
  Hits           211      211             
  Misses          85       85             
  Partials        93       93  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/33?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/33?src=pr&el=footer). Last update [f22fa49...a29515d](https://codecov.io/gh/boostorg/function/pull/33?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
