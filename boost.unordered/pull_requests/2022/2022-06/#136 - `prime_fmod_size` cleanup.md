# #136 `prime_fmod_size` cleanup [Merged]

> Username: cmazakas  
> Created at: 2022-06-30 21:37:53 UTC  
> Updated at: 2022-07-01 15:11:15 UTC  
> Merged at: 2022-07-01 14:58:39 UTC  
> Closed at: 2022-07-01 14:58:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/136  

Pull the `prime_fmod_size` class into its own header file, adjust `fca.hpp` and do some light `#include` cleanup.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-30 23:11:06 UTC  
> Updated_at: 2022-06-30 23:18:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/136#issuecomment-1171760897  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/136?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#136](https://codecov.io/gh/boostorg/unordered/pull/136?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7746518) into [develop](https://codecov.io/gh/boostorg/unordered/commit/45542e26cb9e5968b47d1a47cc32be888d0c9268?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (45542e2) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/136/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/136?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #136   +/-   ##  
========================================  
  Coverage    97.28%   97.28%             
========================================  
  Files           74       75    +1       
  Lines         9942     9943    +1       
========================================  
+ Hits          9672     9673    +1       
  Misses         270      270             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/136?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/fca.hpp](https://codecov.io/gh/boostorg/unordered/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.60% <ø> (-0.04%)` | :arrow_down: |  
| [test/unordered/prime\_fmod\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcHJpbWVfZm1vZF90ZXN0cy5jcHA=) | `94.04% <ø> (ø)` | |  
| [include/boost/unordered/detail/prime\_fmod.hpp](https://codecov.io/gh/boostorg/unordered/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3ByaW1lX2Ztb2QuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/136?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/136?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [45542e2...7746518](https://codecov.io/gh/boostorg/unordered/pull/136?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
