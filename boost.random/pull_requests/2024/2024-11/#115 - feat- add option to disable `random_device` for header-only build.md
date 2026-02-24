# #115 feat: add option to disable `random_device` for header-only build [Merged]

> Username: zjyhjqs  
> Created at: 2024-11-08 16:42:49 UTC  
> Updated at: 2025-03-18 02:13:24 UTC  
> Merged at: 2025-03-17 17:55:39 UTC  
> Closed at: 2025-03-17 17:55:39 UTC  
> Url: https://github.com/boostorg/random/pull/115  

As demonstrated in [documentation](http://www.boost.org/doc/libs/release/more/getting_started/windows.html#header-only-libraries), the random lib can be header-only if `random_device` is disabled.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-11-08 19:38:10 UTC  
> Updated_at: 2025-03-17 17:56:09 UTC  
> Url: https://github.com/boostorg/random/pull/115#issuecomment-2465618773  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/115?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.83%. Comparing base [(`80d0bd5`)](https://app.codecov.io/gh/boostorg/random/commit/80d0bd55193eef2b13eaebaa5f9c2d66f0c22048?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2f8e706`)](https://app.codecov.io/gh/boostorg/random/commit/2f8e706a28c15c84aff5b1c1280dffdcceabcaa1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/115/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #115   +/-   ##  
========================================  
  Coverage    95.83%   95.83%             
========================================  
  Files          114      114             
  Lines         8115     8115             
========================================  
  Hits          7777     7777             
  Misses         338      338             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/115?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/115?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [80d0bd5...2f8e706](https://app.codecov.io/gh/boostorg/random/pull/115?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary>🚀 New features to boost your workflow: </summary>  
  
- ❄ [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: zjyhjqs  
> Created_at: 2025-03-16 11:23:23 UTC  
> Url: https://github.com/boostorg/random/pull/115#issuecomment-2727341173  

Please review the PR and merge it if possible.

---

## Comment 3

> Username: mborland  
> Created_at: 2025-03-17 13:42:45 UTC  
> Url: https://github.com/boostorg/random/pull/115#issuecomment-2729560735  

> Please review the PR and merge it if possible.  
  
I approved the CI run. The change looks fine and is inline with what's already available when building with B2.

---

## Review 4 [Changes requested]

> Username: mborland  
> Created_at: 2025-03-17 16:05:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/random/pull/115#pullrequestreview-2691164228  

Sorry, small nitpick. Can you please make the macro `BOOST_RANDOM_ENABLE_RANDOM_DEVICE`? I think all caps is better than the mixed case that it is right now.

---

## Comment 5

> Username: zjyhjqs  
> Created_at: 2025-03-17 16:14:38 UTC  
> Url: https://github.com/boostorg/random/pull/115#issuecomment-2730102372  

> Sorry, small nitpick. Can you please make the macro `BOOST_RANDOM_ENABLE_RANDOM_DEVICE`? I think all caps is better than the mixed case that it is right now.  
  
done

---

## Review 6 [Approved]

> Username: mborland  
> Created_at: 2025-03-17 17:20:23 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/random/pull/115#pullrequestreview-2691457609  

LGTM. Will merge once the CI finishes cycling. Thanks you!

---
