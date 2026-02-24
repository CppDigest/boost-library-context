# #61 refactor: use core/checked_delete.hpp over checked_delete.hpp [Merged]

> Username: fanquake  
> Created at: 2022-05-17 11:50:55 UTC  
> Updated at: 2022-08-19 09:34:32 UTC  
> Merged at: 2022-05-19 18:24:32 UTC  
> Closed at: 2022-05-19 18:24:32 UTC  
> Url: https://github.com/boostorg/signals2/pull/61  

The later is deprecated.  
  
```cpp  
#ifndef BOOST_CHECKED_DELETE_HPP  
#define BOOST_CHECKED_DELETE_HPP  
  
// The header file at this path is deprecated;  
// use boost/core/checked_delete.hpp instead.  
  
#include <boost/core/checked_delete.hpp>  
  
#endif  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-17 11:57:50 UTC  
> Url: https://github.com/boostorg/signals2/pull/61#issuecomment-1128778344  

# [Codecov](https://codecov.io/gh/boostorg/signals2/pull/61?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#61](https://codecov.io/gh/boostorg/signals2/pull/61?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2ad77ef) into [develop](https://codecov.io/gh/boostorg/signals2/commit/22fe9c0ee5fe069bd076b49965388bf55e1c5dfd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (22fe9c0) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #61   +/-   ##  
========================================  
  Coverage    48.26%   48.26%             
========================================  
  Files           47       47             
  Lines         2215     2215             
  Branches      1096     1096             
========================================  
  Hits          1069     1069             
  Misses         126      126             
  Partials      1020     1020             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/signals2/pull/61?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/signals2/deconstruct\_ptr.hpp](https://codecov.io/gh/boostorg/signals2/pull/61/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9kZWNvbnN0cnVjdF9wdHIuaHBw) | `65.21% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/signals2/pull/61?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/signals2/pull/61?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [22fe9c0...2ad77ef](https://codecov.io/gh/boostorg/signals2/pull/61?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
