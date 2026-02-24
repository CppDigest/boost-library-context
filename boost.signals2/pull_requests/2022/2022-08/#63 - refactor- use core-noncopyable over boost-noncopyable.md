# #63 refactor: use core/noncopyable over boost/noncopyable [Merged]

> Username: fanquake  
> Created at: 2022-08-24 11:40:32 UTC  
> Updated at: 2022-08-24 14:01:48 UTC  
> Merged at: 2022-08-24 14:01:26 UTC  
> Closed at: 2022-08-24 14:01:26 UTC  
> Url: https://github.com/boostorg/signals2/pull/63  

The later is deprecated  
```cpp  
#ifndef BOOST_NONCOPYABLE_HPP  
#define BOOST_NONCOPYABLE_HPP  
  
// The header file at this path is deprecated;  
// use boost/core/noncopyable.hpp instead.  
  
#include <boost/core/noncopyable.hpp>  
  
#endif  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-24 11:47:42 UTC  
> Url: https://github.com/boostorg/signals2/pull/63#issuecomment-1225609670  

# [Codecov](https://codecov.io/gh/boostorg/signals2/pull/63?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#63](https://codecov.io/gh/boostorg/signals2/pull/63?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fda71d0) into [develop](https://codecov.io/gh/boostorg/signals2/commit/49ed1573fa91bb8c60d0a20a5d5e36d8b6d94ea9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (49ed157) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #63   +/-   ##  
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
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/signals2/pull/63?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/signals2/connection.hpp](https://codecov.io/gh/boostorg/signals2/pull/63/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9jb25uZWN0aW9uLmhwcA==) | `74.67% <ø> (ø)` | |  
| [include/boost/signals2/detail/unique\_lock.hpp](https://codecov.io/gh/boostorg/signals2/pull/63/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9kZXRhaWwvdW5pcXVlX2xvY2suaHBw) | `66.66% <ø> (ø)` | |  
| [include/boost/signals2/signal.hpp](https://codecov.io/gh/boostorg/signals2/pull/63/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9zaWduYWwuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/signals2/signal\_base.hpp](https://codecov.io/gh/boostorg/signals2/pull/63/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9zaWduYWxfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/signal\_n\_test.cpp](https://codecov.io/gh/boostorg/signals2/pull/63/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaWduYWxfbl90ZXN0LmNwcA==) | `33.33% <0.00%> (ø)` | |  
  
Help us with your feedback. Take ten seconds to tell us [how you rate us](https://about.codecov.io/nps?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Have a feature suggestion? [Share it here.](https://app.codecov.io/gh/feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)

---
