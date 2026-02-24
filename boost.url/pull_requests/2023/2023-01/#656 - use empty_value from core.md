# #656 use empty_value from core [Merged]

> Username: alandefreitas  
> Created at: 2023-01-11 23:54:01 UTC  
> Updated at: 2023-07-24 14:36:45 UTC  
> Merged at: 2023-01-12 21:42:58 UTC  
> Closed at: 2023-01-12 21:42:58 UTC  
> Url: https://github.com/boostorg/url/pull/656  

fix #639

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-01-12 00:23:42 UTC  
> Url: https://github.com/boostorg/url/pull/656#issuecomment-1379651931  

GCOVR code coverage report [https://656.url.prtest.cppalliance.org/gcovr/index.html](https://656.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://656.url.prtest.cppalliance.org/genhtml/index.html](https://656.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://656.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://656.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-01-12 05:26:00 UTC  
> Url: https://github.com/boostorg/url/pull/656#issuecomment-1379831165  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#656](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (49f3d53) into [develop](https://codecov.io/gh/boostorg/url/commit/af8aa0388769fb2d9c44a9ef8919acd215739df3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (af8aa03) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/656/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #656      +/-   ##  
===========================================  
- Coverage    96.88%   96.87%   -0.01%       
===========================================  
  Files          147      146       -1       
  Lines         7924     7910      -14       
===========================================  
- Hits          7677     7663      -14       
  Misses         247      247                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/over\_allocator.hpp](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL292ZXJfYWxsb2NhdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/optional\_rule.hpp](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9vcHRpb25hbF9ydWxlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/detail/tuple.hpp](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZXRhaWwvdHVwbGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/impl/range\_rule.hpp](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3JhbmdlX3J1bGUuaHBw) | `93.80% <100.00%> (ø)` | |  
| [include/boost/url/grammar/tuple\_rule.hpp](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci90dXBsZV9ydWxlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [af8aa03...49f3d53](https://codecov.io/gh/boostorg/url/pull/656?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2023-01-12 21:32:17 UTC  
> Url: https://github.com/boostorg/url/pull/656#issuecomment-1381017368  

You don't need my review for this

---
