# #129 Fix off-by-one error used by the `prime_fmod_size` policy [Closed]

> Username: cmazakas  
> Created at: 2022-06-21 22:36:52 UTC  
> Updated at: 2022-06-22 21:49:01 UTC  
> Closed at: 2022-06-22 21:49:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/129  

Unfortunately, comparing against `inv_sizes32_len` yields incorrect results when compared directly against rote modulo.  
  
Subtracting by 1 appropriately fixes the arithemtic.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-22 02:45:06 UTC  
> Updated_at: 2022-06-22 20:20:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/129#issuecomment-1162569150  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/129?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#129](https://codecov.io/gh/boostorg/unordered/pull/129?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4a13097) into [develop](https://codecov.io/gh/boostorg/unordered/commit/e1dff1c93131f10cd941a11d8774b8e2c1c5a2e3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e1dff1c) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 4a13097 differs from pull request most recent head fbca485. Consider uploading reports for the commit fbca485 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/129/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #129      +/-   ##  
===========================================  
+ Coverage    97.30%   97.31%   +0.01%       
===========================================  
  Files           73       74       +1       
  Lines         9858     9866       +8       
===========================================  
+ Hits          9592     9601       +9       
+ Misses         266      265       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/fca.hpp](https://codecov.io/gh/boostorg/unordered/pull/129/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.64% <100.00%> (+0.35%)` | :arrow_up: |  
| [test/unordered/detail\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/129/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZGV0YWlsX3Rlc3RzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/129?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/129?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e1dff1c...fbca485](https://codecov.io/gh/boostorg/unordered/pull/129?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
