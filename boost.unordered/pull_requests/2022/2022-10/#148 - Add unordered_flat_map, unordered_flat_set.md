# #148 Add unordered_flat_map, unordered_flat_set [Merged]

> Username: cmazakas  
> Created at: 2022-10-24 22:06:44 UTC  
> Updated at: 2022-10-26 16:58:08 UTC  
> Merged at: 2022-10-26 16:58:08 UTC  
> Closed at: 2022-10-26 16:58:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/148  

Add implementations of new open-addressing containers and update tests accordingly

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-25 02:34:38 UTC  
> Updated_at: 2022-10-25 17:54:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/148#issuecomment-1289896072  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/148?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#148](https://codecov.io/gh/boostorg/unordered/pull/148?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7db310f) into [develop](https://codecov.io/gh/boostorg/unordered/commit/668abe4b0ae94907c08f836480c87d820a737c50?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (668abe4) will **increase** coverage by `0.12%`.  
> The diff coverage is `98.18%`.  
  
> :exclamation: Current head 7db310f differs from pull request most recent head fb31525. Consider uploading reports for the commit fb31525 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/148/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/148?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #148      +/-   ##  
===========================================  
+ Coverage    97.49%   97.61%   +0.12%       
===========================================  
  Files           75       81       +6       
  Lines        10540    11846    +1306       
===========================================  
+ Hits         10276    11564    +1288       
- Misses         264      282      +18       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/148?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.16% <ø> (ø)` | |  
| [test/exception/swap\_exception\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vc3dhcF9leGNlcHRpb25fdGVzdHMuY3Bw) | `97.50% <ø> (+12.50%)` | :arrow_up: |  
| [test/helpers/invariants.hpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9oZWxwZXJzL2ludmFyaWFudHMuaHBw) | `75.00% <ø> (ø)` | |  
| [test/unordered/incomplete\_test.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvaW5jb21wbGV0ZV90ZXN0LmNwcA==) | `58.62% <ø> (+1.47%)` | :arrow_up: |  
| [test/unordered/insert\_hint\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvaW5zZXJ0X2hpbnRfdGVzdHMuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/unordered/scoped\_allocator.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvc2NvcGVkX2FsbG9jYXRvci5jcHA=) | `93.75% <ø> (-6.25%)` | :arrow_down: |  
| [test/unordered/transparent\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvdHJhbnNwYXJlbnRfdGVzdHMuY3Bw) | `91.90% <33.33%> (ø)` | |  
| [test/unordered/scary\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvc2NhcnlfdGVzdHMuY3Bw) | `71.42% <50.00%> (-7.74%)` | :arrow_down: |  
| [test/unordered/emplace\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZW1wbGFjZV90ZXN0cy5jcHA=) | `96.13% <93.33%> (-0.64%)` | :arrow_down: |  
| [test/unordered/init\_type\_insert\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvaW5pdF90eXBlX2luc2VydF90ZXN0cy5jcHA=) | `95.87% <95.87%> (ø)` | |  
| ... and [44 more](https://codecov.io/gh/boostorg/unordered/pull/148/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/148?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/148?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [668abe4...fb31525](https://codecov.io/gh/boostorg/unordered/pull/148?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
