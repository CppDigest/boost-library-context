# #1299 Add manual promotion to arcsine, beta, and Bernoulli distributions [Open]

> Username: mborland  
> Created at: 2025-08-14 16:39:07 UTC  
> Updated at: 2025-12-08 15:28:28 UTC  
> Url: https://github.com/boostorg/math/pull/1299  

This is an extension to #1294 since it looks like most of the distributions have functions where manual promotion needs to be applied to follow what the policy says. I tried to reduce the ugliness as much as possible, but the result is that the code is generally less readable than it is without this PR. Thoughts @jzmaddock and @WarrenWeckesser. If there's no real opposition I can continue plugging along with this.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-14 17:33:53 UTC  
> Updated_at: 2025-12-08 15:28:28 UTC  
> Url: https://github.com/boostorg/math/pull/1299#issuecomment-3189311485  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1299?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.47%. Comparing base ([`483f36c`](https://app.codecov.io/gh/boostorg/math/commit/483f36c067d659cb833c7c03fd3dd05bb1fb1814?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2852b4b`](https://app.codecov.io/gh/boostorg/math/commit/2852b4b61920ba517d9c454a2bea186688cfb89e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 157 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1299/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1299?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1299      +/-   ##  
===========================================  
- Coverage    95.07%   94.47%   -0.60%       
===========================================  
  Files          796      686     -110       
  Lines        67020    49438   -17582       
===========================================  
- Hits         63717    46705   -17012       
+ Misses        3303     2733     -570       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1299?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1299?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvYmV0YS5ocHA=) | `77.27% <ø> (-8.16%)` | :arrow_down: |  
| [include/boost/math/policies/policy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1299?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fpolicies%2Fpolicy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3BvbGljaWVzL3BvbGljeS5ocHA=) | `95.83% <ø> (ø)` | |  
  
... and [138 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1299/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1299?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1299?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [483f36c...2852b4b](https://app.codecov.io/gh/boostorg/math/pull/1299?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
