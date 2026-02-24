# #110 Make boost::iostreams::detail::path constructible from std::filesystem::path [Open]

> Username: nikola-sh  
> Created at: 2020-02-27 10:26:59 UTC  
> Updated at: 2025-10-21 19:34:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-27 11:38:02 UTC  
> Updated_at: 2025-10-21 19:34:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#issuecomment-591926435  

## [Codecov](https://app.codecov.io/gh/boostorg/iostreams/pull/110?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 68.85%. Comparing base ([`bc86890`](https://app.codecov.io/gh/boostorg/iostreams/commit/bc868902b0e42a0ff7440d52c584a14a04d0fad3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`126d851`](https://app.codecov.io/gh/boostorg/iostreams/commit/126d85124394bda3ac1f85adef86cfda2e9820ee?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 75 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #110   +/-   ##  
========================================  
  Coverage    68.84%   68.85%             
========================================  
  Files           80       80             
  Lines         3444     3445    +1       
  Branches      1027     1027             
========================================  
+ Hits          2371     2372    +1       
  Misses         454      454             
  Partials       619      619             
```  
</details>  
  
[:umbrella: View full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/iostreams/pull/110?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).     
:loudspeaker: Have feedback on the report? [Share it here](https://about.codecov.io/codecov-pr-comment-feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: nikola-sh  
> Created_at: 2020-03-02 12:10:32 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#issuecomment-593371997  

@jeking3 Take a look at this PR please

---

## Comment 3

> Username: nikola-sh  
> Created_at: 2020-05-17 19:49:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#issuecomment-629850827  

@mclow Take a look at this PR please

---

## Comment 4

> Username: mclow  
> Created_at: 2020-05-18 14:45:04 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#issuecomment-630230577  

I don't see any tests for this new functionality. The code looks reasonable, but w/o tests, do we know that it works?

---

## Review 5 [Commented]

> Username: nikola-sh  
> Created_at: 2020-05-19 16:37:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/110#pullrequestreview-414628300  

📁 test/Jamfile.v2

```diff
 103 |+                 path_test.cpp
 104 |+                     : [ check-target-builds ../../config/checks//cpp_lib_filesystem
 105 |+                         : [ check-target-builds can_use_stdcxxfs : <source>stdc++fs : [ check-target-builds can_use_cxxfs : <source>c++fs ] ]
```

> Username: nikola-sh  
> Created_at: 2020-05-19 16:37:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#discussion_r427442457  

early gcc and clang versions requires to link specific filesystem library


---

## Comment 6

> Username: nikola-sh  
> Created_at: 2020-05-19 18:29:22 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#issuecomment-631000808  

@mclow  I have added a test.  
PR is failed because one job is failed with error `Error: retrieving gpg key timed out`. Could you review a code and rerun failed job?

---

## Comment 7

> Username: nikola-sh  
> Created_at: 2020-05-28 12:24:37 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#issuecomment-635310054  

@mclow  Take a look at this PR again please

---

## Comment 8

> Username: timblechmann  
> Created_at: 2023-09-20 06:33:29 UTC  
> Url: https://github.com/boostorg/iostreams/pull/110#issuecomment-1727053725  

any update on this one?

---
