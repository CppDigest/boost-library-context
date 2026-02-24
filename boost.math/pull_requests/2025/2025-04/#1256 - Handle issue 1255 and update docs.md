# #1256 Handle issue 1255 and update docs [Merged]

> Username: ckormanyos  
> Created at: 2025-04-11 10:49:49 UTC  
> Updated at: 2025-04-12 21:56:11 UTC  
> Merged at: 2025-04-12 17:09:01 UTC  
> Closed at: 2025-04-12 17:09:01 UTC  
> Url: https://github.com/boostorg/math/pull/1256  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-04-11 10:50:01 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2796557902  

See also #1255

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-04-12 06:07:02 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2798515243  

Something weird is happening in CI, in this PR and also in the CI that ran when @mborland simply incremented the library version [here](https://github.com/boostorg/math/actions/runs/14402716458).  
  
It seems like something is influencing files  that were not touched in the changes.  
  
Cc: @jzmaddock

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-04-12 08:06:44 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2798728105  

For some reason, when exceptions are off at compilation time, we are hitting [this line](https://github.com/boostorg/exception/blob/9238ef8e7f97e619ed3e58bc7becf16b4966ccf0/include/boost/exception/detail/clone_current_exception.hpp#L22). Then certain tests in the Math test suite fail when Boost has no exceptions yet this file is still  included.  
  
That line is, however, two years old. I wonder what tiny change and where causes this phenomenon today?

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-04-12 08:14:34 UTC  
> Updated_at: 2025-04-12 08:14:56 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2798731247  

Hmmm I don't know so much about the dependencies in the Jamfile. But why is Math's test suite trying to build the `libboost_exception.a` archive [here](https://github.com/boostorg/math/actions/runs/14402716458/job/40391903290#step:17:2940)?  
  
This is in the recent version increment from @mborland

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2025-04-12 08:35:40 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2798738157  

There is some cruft in the Jamfile, but there are other issues too.  I'm working through them in https://github.com/boostorg/math/pull/1254, it's getting closer to green, but there are no cigars being lit just yet!

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-04-12 11:28:25 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2798794273  

> some cruft in the Jamfile, but there are other issues too. I'm working through them in https://github.com/boostorg/math/pull/1254, it's getting closer to green,  
  
Thanks John. OK cool. I'll hold my horses a bit and wait till that goes green and gets in develop.  
  
In the meantime, I'll try to ensure that the new tests for #1255 are all passing.

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2025-04-12 13:53:00 UTC  
> Updated_at: 2025-04-12 21:56:10 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2798840346  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1256?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.83%. Comparing base [(`e818106`)](https://app.codecov.io/gh/boostorg/math/commit/e8181069f836664fb57c288f1e367dbbbd967f9b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`76dede0`)](https://app.codecov.io/gh/boostorg/math/commit/76dede030e0937dc491d51c8b3d9b845b638c4d6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1256/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1256?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1256   +/-   ##  
========================================  
  Coverage    93.83%   93.83%             
========================================  
  Files          657      658    +1       
  Lines        55244    55256   +12       
========================================  
+ Hits         51840    51852   +12       
  Misses        3404     3404             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1256?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/math/special\_functions/detail/bessel\_yn.hpp](https://app.codecov.io/gh/boostorg/math/pull/1256?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_yn.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfeW4uaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/git\_issue\_1255.cpp](https://app.codecov.io/gh/boostorg/math/pull/1256?src=pr&el=tree&filepath=test%2Fgit_issue_1255.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTI1NS5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1256?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1256?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e818106...76dede0](https://app.codecov.io/gh/boostorg/math/pull/1256?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2025-04-12 17:08:08 UTC  
> Url: https://github.com/boostorg/math/pull/1256#issuecomment-2798909984  

It it is good enough for #1254, it's good enough for #1255, with the repeated comment, OK looks good to go, only failures are from cycl Cauchy tests.  
  
We'll let @mborland look at those.

---
