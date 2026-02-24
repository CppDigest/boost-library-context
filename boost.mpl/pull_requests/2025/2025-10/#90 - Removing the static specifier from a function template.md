# #90 Removing the static specifier from a function template [Merged]

> Username: d7d1cd  
> Created at: 2025-10-14 16:20:20 UTC  
> Updated at: 2025-10-23 08:38:33 UTC  
> Merged at: 2025-10-23 08:37:57 UTC  
> Closed at: 2025-10-23 08:37:57 UTC  
> Url: https://github.com/boostorg/mpl/pull/90  

The presence of the static specifier on a template makes it internally linked. This template is used, in particular, to evaluate the NTTP boost::mpl_::bool_, which is needed in boost::multi_index::tag. Internal linkage prevents the use of boost::multi_index::tag and everything that depends on it in the global fragment of C++20 modules.  
  
MRE here https://godbolt.org/z/efM6oaex9

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-10-23 08:13:46 UTC  
> Url: https://github.com/boostorg/mpl/pull/90#issuecomment-3435673005  

@pdimov could you please take a look at this fix and merge if it is OK?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-10-23 08:20:30 UTC  
> Updated_at: 2025-10-23 08:38:33 UTC  
> Url: https://github.com/boostorg/mpl/pull/90#issuecomment-3435696939  

## [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/90?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.73%. Comparing base ([`02ec362`](https://app.codecov.io/gh/boostorg/mpl/commit/02ec362f5ca4907290e1516adcc53bf7673a4806?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`67180b0`](https://app.codecov.io/gh/boostorg/mpl/commit/67180b07d384ab6ab5b4a57d2a45689943b97818?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mpl/pull/90/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mpl/pull/90?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #90   +/-   ##  
========================================  
  Coverage    94.73%   94.73%             
========================================  
  Files            5        5             
  Lines           19       19             
  Branches         3        3             
========================================  
  Hits            18       18             
  Misses           1        1             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mpl/pull/90?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/90?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [02ec362...67180b0](https://app.codecov.io/gh/boostorg/mpl/pull/90?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
