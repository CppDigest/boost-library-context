# #28 Use BOOST_OVERRIDE [Merged]

> Username: EugeneZelenko  
> Created at: 2020-05-23 00:58:16 UTC  
> Updated at: 2025-06-08 16:03:58 UTC  
> Merged at: 2025-06-08 16:03:51 UTC  
> Closed at: 2025-06-08 16:03:51 UTC  
> Url: https://github.com/boostorg/contract/pull/28  

Use BOOST_OVERRIDE to fix GCC -Wsuggest-override and Clang-tidy modernize-use-override warnings.  
  
Also fix Clang-tidy readability-container-size-empty and readability-redundant-string-init warnings.  
  
This fixes #27

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-23 02:36:25 UTC  
> Updated_at: 2025-06-08 16:03:57 UTC  
> Url: https://github.com/boostorg/contract/pull/28#issuecomment-632973088  

## [Codecov](https://app.codecov.io/gh/boostorg/contract/pull/28?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `0%` with `3 lines` in your changes missing coverage. Please review.  
> Project coverage is 63.23%. Comparing base [(`e0ea5f5`)](https://app.codecov.io/gh/boostorg/contract/commit/e0ea5f5d71a5ab420518980b92eecfd6f3fe45b8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0ab4125`)](https://app.codecov.io/gh/boostorg/contract/commit/0ab41258205936066c8443c6688363e18935aa76?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 25 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/contract/pull/28?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...e/boost/contract/detail/inlined/core/exception.hpp](https://app.codecov.io/gh/boostorg/contract/pull/28?src=pr&el=tree&filepath=include%2Fboost%2Fcontract%2Fdetail%2Finlined%2Fcore%2Fexception.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jb250cmFjdC9kZXRhaWwvaW5saW5lZC9jb3JlL2V4Y2VwdGlvbi5ocHA=) | 0.00% | [1 Missing and 2 partials :warning: ](https://app.codecov.io/gh/boostorg/contract/pull/28?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #28   +/-   ##  
========================================  
  Coverage    63.23%   63.23%             
========================================  
  Files           30       30             
  Lines          718      718             
  Branches       279      286    +7       
========================================  
  Hits           454      454             
  Misses         108      108             
  Partials       156      156             
```  
  
</details>  
  
[:umbrella: View full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/contract/pull/28?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).     
:loudspeaker: Have feedback on the report? [Share it here](https://about.codecov.io/codecov-pr-comment-feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
