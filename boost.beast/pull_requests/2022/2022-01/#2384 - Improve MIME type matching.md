# #2384 Improve MIME type matching [Closed]

> Username: ho-229  
> Created at: 2022-01-26 07:13:50 UTC  
> Updated at: 2022-05-03 01:14:37 UTC  
> Closed at: 2022-05-03 01:14:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2384  

Use boost::unordered_map::find to match the MIME type

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-01-26 07:38:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2384#issuecomment-1021940708  

![pullrequest](https://2384.beast.tracing.cppalliance.org/pullrequest-8d7c809b.png)  
Timeline tracing charts: [https://2384.beast.tracing.cppalliance.org/index.html](https://2384.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-02-01 16:42:51 UTC  
> Updated_at: 2022-02-01 17:03:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2384#issuecomment-1027047242  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2384?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2384](https://codecov.io/gh/boostorg/beast/pull/2384?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9dde65f) into [develop](https://codecov.io/gh/boostorg/beast/commit/5bdd1efb8be740bb5425adc054c990818134f3e2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5bdd1ef) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2384/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2384?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2384   +/-   ##  
========================================  
  Coverage    94.88%   94.89%             
========================================  
  Files          151      151             
  Lines        12230    12230             
========================================  
+ Hits         11605    11606    +1       
+ Misses         625      624    -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2384?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2384/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `95.61% <0.00%> (+0.87%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2384?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2384?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5bdd1ef...9dde65f](https://codecov.io/gh/boostorg/beast/pull/2384?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: sehe  
> Created_at: 2022-05-02 22:01:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2384#issuecomment-1115413145  

Can you describe in what way the proposed implementation is improved, and why it matters.  
  
As it stands I see negatives as well as positives. E.g. if you care about efficiency, then perhaps look at the implementations with benchmarks given here: https://stackoverflow.com/a/48235134/85371  
  
As you can probably see, the current implementation is taking the "do no unnecessary work" and "avoid clever code" route. I'm curious what reasons you see why we should consider the switch.

---

## Comment 4

> Username: ho-229  
> Created_at: 2022-05-03 01:14:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2384#issuecomment-1115522270  

Well, thank you for your review.

---
