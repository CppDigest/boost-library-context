# #2682 headers using std::int8_t et al. include <csdint>. [Merged]

> Username: klemens-morgenstern  
> Created at: 2023-05-12 12:56:01 UTC  
> Updated at: 2023-05-15 00:49:53 UTC  
> Merged at: 2023-05-15 00:49:53 UTC  
> Closed at: 2023-05-15 00:49:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2682  

Implements #2676 .  
  
@ednolan can you confirm this does it?

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-05-12 13:24:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2682#issuecomment-1545741368  

![pullrequest](https://2682.beast.tracing.cppalliance.org/pullrequest-fb82f407.png)  
Timeline tracing charts: [https://2682.beast.tracing.cppalliance.org/index.html](https://2682.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-05-12 13:58:52 UTC  
> Updated_at: 2023-05-12 14:16:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2682#issuecomment-1545793929  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2682](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8ff6521) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/e65aff42f52f610cabb617484f8921d970f18f18?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e65aff4) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2682/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2682   +/-   ##  
========================================  
  Coverage    92.97%   92.97%             
========================================  
  Files          177      177             
  Lines        13658    13658             
========================================  
  Hits         12698    12698             
  Misses         960      960             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `94.11% <ø> (ø)` | |  
| [include/boost/beast/http/buffer\_body.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `88.63% <ø> (ø)` | |  
| [include/boost/beast/http/detail/rfc7230.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/empty\_body.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `85.71% <ø> (ø)` | |  
| [include/boost/beast/http/impl/field.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `97.64% <ø> (ø)` | |  
| [include/boost/beast/http/parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `86.95% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e65aff4...8ff6521](https://app.codecov.io/gh/boostorg/beast/pull/2682?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
