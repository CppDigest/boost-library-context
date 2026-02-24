# #206 re-enable sanitize builds and suppress known serialization issues [Closed]

> Username: jeking3  
> Created at: 2022-04-24 22:08:50 UTC  
> Updated at: 2022-04-26 02:53:31 UTC  
> Closed at: 2022-04-26 02:53:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/206  

There are multiple serialization ubsan issues already recorded in that project's issue list.  We'll ignore them but still get the benefit of running ubsan on date_time.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-04-25 04:46:42 UTC  
> Updated_at: 2022-04-26 01:01:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/206#issuecomment-1108074521  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/206?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#206](https://codecov.io/gh/boostorg/date_time/pull/206?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (770581e) into [develop](https://codecov.io/gh/boostorg/date_time/commit/cbad9bcec919b9ad7382f834d3e3de9f480568a7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cbad9bc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/206/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/date_time/pull/206?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #206   +/-   ##  
========================================  
  Coverage    91.74%   91.74%             
========================================  
  Files           77       77             
  Lines         4823     4823             
========================================  
  Hits          4425     4425             
  Misses         398      398             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/206?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/206?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cbad9bc...770581e](https://codecov.io/gh/boostorg/date_time/pull/206?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: jeking3  
> Created_at: 2022-04-26 02:53:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/206#issuecomment-1109246416  

Sadly I could not find a way to suppress https://github.com/boostorg/serialization/issues/193 so I have to leave the sanitizer builds disabled.

---
