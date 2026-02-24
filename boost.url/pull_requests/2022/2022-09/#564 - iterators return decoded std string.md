# #564 iterators return decoded std string [Merged]

> Username: vinniefalco  
> Created at: 2022-09-20 01:23:42 UTC  
> Updated at: 2022-09-20 03:58:38 UTC  
> Merged at: 2022-09-20 03:58:21 UTC  
> Closed at: 2022-09-20 03:58:21 UTC  
> Url: https://github.com/boostorg/url/pull/564  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-20 01:41:04 UTC  
> Updated_at: 2022-09-20 01:59:03 UTC  
> Url: https://github.com/boostorg/url/pull/564#issuecomment-1251737185  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/564?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#564](https://codecov.io/gh/boostorg/url/pull/564?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (dd62491) into [develop](https://codecov.io/gh/boostorg/url/commit/7f9231721bd87a9975c71a1ef7dc8020b1674188?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7f92317) will **decrease** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/564/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/564?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #564      +/-   ##  
===========================================  
- Coverage    96.79%   96.75%   -0.04%       
===========================================  
  Files          138      138                
  Lines         6769     6695      -74       
===========================================  
- Hits          6552     6478      -74       
  Misses         217      217                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/564?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/params\_base.ipp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfYmFzZS5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded\_base.hpp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF9iYXNlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_base.hpp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19iYXNlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_encoded\_base.hpp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19lbmNvZGVkX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_base.hpp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/params\_ref.ipp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfcmVmLmlwcA==) | `65.51% <100.00%> (-1.88%)` | :arrow_down: |  
| [include/boost/url/impl/segments\_base.ipp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19iYXNlLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/param.hpp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW0uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/impl/recycled.hpp](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3JlY3ljbGVkLmhwcA==) | `87.71% <0.00%> (-0.81%)` | :arrow_down: |  
| ... and [5 more](https://codecov.io/gh/boostorg/url/pull/564/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/564?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/564?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7f92317...dd62491](https://codecov.io/gh/boostorg/url/pull/564?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
