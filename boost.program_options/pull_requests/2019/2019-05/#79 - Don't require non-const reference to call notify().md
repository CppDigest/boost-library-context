# #79 Don't require non-const reference to call notify() [Open]

> Username: scramsby  
> Created at: 2019-05-08 23:58:59 UTC  
> Updated at: 2025-10-21 19:32:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/79  

This fixes notify() and variables_map::notify() to not require a non-const reference to the variables_map. This is useful if you are passing this object from one module to another and want to trigger handling of those variables in the new module's context without enabling that new module to mutate the shared map's state.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-05-09 07:16:59 UTC  
> Updated_at: 2025-10-21 19:32:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/79#issuecomment-490776596  

## [Codecov](https://app.codecov.io/gh/boostorg/program_options/pull/79?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `66.66667%` with `1 line` in your changes missing coverage. Please review.  
:warning: Please [upload](https://docs.codecov.com/docs/codecov-uploader) report for BASE (`develop@d95d316`). [Learn more](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit) about missing BASE report.  
:warning: Report is 134 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/program_options/pull/79?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [src/variables\_map.cpp](https://app.codecov.io/gh/boostorg/program_options/pull/79?src=pr&el=tree&filepath=src%2Fvariables_map.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3ZhcmlhYmxlc19tYXAuY3Bw) | 66.66% | [0 Missing and 1 partial :warning: ](https://app.codecov.io/gh/boostorg/program_options/pull/79?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #79   +/-   ##  
==========================================  
  Coverage           ?   50.03%             
==========================================  
  Files              ?       23             
  Lines              ?     1387             
  Branches           ?      707             
==========================================  
  Hits               ?      694             
  Misses             ?      110             
  Partials           ?      583             
```  
</details>  
  
[:umbrella: View full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/program_options/pull/79?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).     
:loudspeaker: Have feedback on the report? [Share it here](https://about.codecov.io/codecov-pr-comment-feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
