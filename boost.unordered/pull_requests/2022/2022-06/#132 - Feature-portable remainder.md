# #132 Feature/portable remainder [Merged]

> Username: joaquintides  
> Created at: 2022-06-26 19:36:41 UTC  
> Updated at: 2024-08-17 16:48:57 UTC  
> Merged at: 2022-06-26 23:24:51 UTC  
> Closed at: 2022-06-26 23:24:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/132  

Used Peter's portable `get_remainder` and provided `fast_modulo` if and only if in a 64-bit architecture. Removed `BOOST_UNORDERED_FCA_FASTMOD_SUPPORT` as it is now synonymous with `BOOST_UNORDERED_FCA_HAS_64B_SIZE_T`. Synced `prime_fmod_tests.cpp` with the changes.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-26 21:55:29 UTC  
> Updated_at: 2022-06-26 22:16:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/132#issuecomment-1166655875  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/132?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#132](https://codecov.io/gh/boostorg/unordered/pull/132?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fb73348) into [develop](https://codecov.io/gh/boostorg/unordered/commit/d49eda63f8de5fdd882a8635622bfc4b52d89772?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d49eda6) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/132/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/132?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #132      +/-   ##  
===========================================  
+ Coverage    97.25%   97.28%   +0.02%       
===========================================  
  Files           74       74                
  Lines         9957     9942      -15       
===========================================  
- Hits          9684     9672      -12       
+ Misses         273      270       -3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/132?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/fca.hpp](https://codecov.io/gh/boostorg/unordered/pull/132/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.64% <100.00%> (ø)` | |  
| [test/unordered/prime\_fmod\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/132/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcHJpbWVfZm1vZF90ZXN0cy5jcHA=) | `94.04% <100.00%> (+2.12%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/132?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/132?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d49eda6...fb73348](https://codecov.io/gh/boostorg/unordered/pull/132?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
