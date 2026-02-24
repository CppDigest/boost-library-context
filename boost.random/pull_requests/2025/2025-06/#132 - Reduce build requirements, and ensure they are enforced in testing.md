# #132 Reduce build requirements, and ensure they are enforced in testing [Merged]

> Username: mborland  
> Created at: 2025-06-16 17:08:48 UTC  
> Updated at: 2025-06-16 20:44:33 UTC  
> Merged at: 2025-06-16 19:19:45 UTC  
> Closed at: 2025-06-16 19:19:45 UTC  
> Url: https://github.com/boostorg/random/pull/132  

Closes: #131

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-16 17:29:13 UTC  
> Updated_at: 2025-06-16 19:19:55 UTC  
> Url: https://github.com/boostorg/random/pull/132#issuecomment-2977449694  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/132?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.83%. Comparing base [(`7363de5`)](https://app.codecov.io/gh/boostorg/random/commit/7363de5e94a524822cd26e04767b84690cca94b1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ee39122`)](https://app.codecov.io/gh/boostorg/random/commit/ee39122450f617d50ff2282a3bcef58452394a89?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/132/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/132?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #132   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/132?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/132?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7363de5...ee39122](https://app.codecov.io/gh/boostorg/random/pull/132?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-06-16 20:44:33 UTC  
> Url: https://github.com/boostorg/random/pull/132#issuecomment-2978075382  

Note that you should normally not need to add any requirements to tests (or any other downstream users of the library), beyond those which are required by the tests themselves. For that, you would add the requirements that need to be fulfilled by the library users in the `usage-requirements` of the library. That is, `requirements` is what library itself needs for compiling, and `usage-requirements` is what users need to fulfill in order to consume the library. For header-only libraries, those are the same.

---
