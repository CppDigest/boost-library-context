# #632 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:31:29 UTC  
> Updated at: 2025-06-10 16:47:50 UTC  
> Merged at: 2025-06-10 14:21:15 UTC  
> Closed at: 2025-06-10 14:21:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
- https://github.com/boostorg/math/pull/1160  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-09-14 13:42:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#issuecomment-2350996110  

Toggling to refresh build now that the deps are available.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-01-12 11:23:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#issuecomment-2585693730  

@grafikrobot is this ready now?  Do we know what the CI failures are?

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-04-05 04:29:43 UTC  
> Updated_at: 2025-06-10 14:21:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#issuecomment-2780203253  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`96eb274`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/96eb274ac6bea385f435e743d8206275ff56a184?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`18969f4`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/18969f4b177f4e982c9d553272327b44428f3a90?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/632/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #632     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          280     280               
  Lines        29074   29074               
=========================================  
- Hits         27349   27347      -2       
- Misses        1725    1727      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_arithmetic\_complex128.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_complex128.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY29tcGxleDEyOC5jcHA=) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_complex\_adaptor.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_complex_adaptor.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY29tcGxleF9hZGFwdG9yLmNwcA==) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_complex\_adaptor\_2.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_complex_adaptor_2.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY29tcGxleF9hZGFwdG9yXzIuY3Bw) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_1.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_cpp_bin_float_1.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF8xLmNwcA==) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_2.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_cpp_bin_float_2.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF8yLmNwcA==) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_2m.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_cpp_bin_float_2m.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF8ybS5jcHA=) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_3.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_cpp_bin_float_3.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF8zLmNwcA==) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_4.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_cpp_bin_float_4.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF80LmNwcA==) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_5.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_cpp_bin_float_5.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF81LmNwcA==) | `100.0% <ø> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_6.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree&filepath=test%2Ftest_arithmetic_cpp_bin_float_6.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF82LmNwcA==) | `100.0% <ø> (ø)` | |  
| ... and [2 more](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/632/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [96eb274...18969f4](https://app.codecov.io/gh/boostorg/multiprecision/pull/632?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2025-05-06 04:39:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#issuecomment-2853257371  

> @grafikrobot is this ready now? Do we know what the CI failures are?  
  
I tried to fix the latest CircleCI failure from the inspect change. Unfortunately I can't fix ICE errors from trying to build the doc tools.  
  
I don't know what the Windows failures are. I'm still investigating them.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2025-06-10 00:09:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#issuecomment-2957337916  

@jzmaddock I finally figured out the issue with the Windows builds. For not an entirely known reason the GMP config check was succeeding on Windows with the use of `searched-lib gmp`. I reverted those changes as they where for a n0 longer needed workaround.

---

## Comment 6

> Username: mborland  
> Created_at: 2025-06-10 14:20:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#issuecomment-2959451270  

CircleCI error is known and unrelated with PR here: https://github.com/boostorg/multiprecision/pull/687

---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2025-06-10 16:46:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/632#pullrequestreview-2914352891  

📁 performance/Jamfile.v2

```diff
  79 |             /boost/system//boost_system
  76 |-           : release
  80 |+           :
```

> Username: jzmaddock  
> Created_at: 2025-06-10 16:46:54 UTC  
> Updated_at: 2025-06-10 16:46:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#discussion_r2138358707  

Question: why have all the performance tests had their "release" tag removed?

> Username: jzmaddock  
> Created_at: 2025-06-10 16:47:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/632#discussion_r2138360121  

Oh I see, it's been made a project requirement.  OK I guess.


---
