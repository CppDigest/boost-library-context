# #216 Avoid using "likely" function as multiple projects define a "likely" macro [Merged]

> Username: apolukhin  
> Created at: 2022-09-08 09:42:38 UTC  
> Updated at: 2022-09-08 11:07:10 UTC  
> Merged at: 2022-09-08 11:01:15 UTC  
> Closed at: 2022-09-08 11:01:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/216  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-08 10:56:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/216#issuecomment-1240556971  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/216?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#216](https://codecov.io/gh/boostorg/date_time/pull/216?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (268127d) into [develop](https://codecov.io/gh/boostorg/date_time/commit/b81ed2874f34f5cb2473018e53db9d9af402ee80?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b81ed28) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/216/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/date_time/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #216   +/-   ##  
========================================  
  Coverage    91.75%   91.75%             
========================================  
  Files           77       77             
  Lines         4827     4827             
========================================  
  Hits          4429     4429             
  Misses         398      398             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/special\_values\_parser.hpp](https://codecov.io/gh/boostorg/date_time/pull/216/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3BlY2lhbF92YWx1ZXNfcGFyc2VyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/date\_time/time\_parsing.hpp](https://codecov.io/gh/boostorg/date_time/pull/216/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9wYXJzaW5nLmhwcA==) | `98.18% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/216?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/216?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b81ed28...268127d](https://codecov.io/gh/boostorg/date_time/pull/216?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: JeffGarland  
> Created_at: 2022-09-08 11:00:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/216#issuecomment-1240561391  

I'm going to agree to this change even though it's the projects defining a macro called likely that are in the wrong -- that's evil and they should know better.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2022-09-08 11:07:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/216#issuecomment-1240567585  

Thanks!  
  
And yes, lower case macro are pure crystallized evil

---
