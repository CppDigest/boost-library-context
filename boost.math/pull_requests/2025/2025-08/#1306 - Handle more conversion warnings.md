# #1306 Handle more conversion warnings [Merged]

> Username: ckormanyos  
> Created at: 2025-08-22 07:40:45 UTC  
> Updated at: 2025-08-22 23:32:11 UTC  
> Merged at: 2025-08-22 19:10:47 UTC  
> Closed at: 2025-08-22 19:10:47 UTC  
> Url: https://github.com/boostorg/math/pull/1306  

In this PR we handle a few more trivial conversion warnings in new and old code

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-22 11:25:25 UTC  
> Updated_at: 2025-08-22 23:32:11 UTC  
> Url: https://github.com/boostorg/math/pull/1306#issuecomment-3214034180  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1306?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:warning: Please [upload](https://docs.codecov.com/docs/codecov-uploader) report for BASE (`develop@73d7523`). [Learn more](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit) about missing BASE report.  
:warning: Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1306/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1306?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff             @@  
##             develop    #1306   +/-   ##  
==========================================  
  Coverage           ?   95.10%             
==========================================  
  Files              ?      796             
  Lines              ?    67115             
  Branches           ?        0             
==========================================  
  Hits               ?    63830             
  Misses             ?     3285             
  Partials           ?        0             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1306?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/differential\_evolution\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1306?src=pr&el=tree&filepath=test%2Fdifferential_evolution_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9kaWZmZXJlbnRpYWxfZXZvbHV0aW9uX3Rlc3QuY3Bw) | `59.70% <ø> (ø)` | |  
| [test/jso\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1306?src=pr&el=tree&filepath=test%2Fjso_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9qc29fdGVzdC5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/random\_search\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1306?src=pr&el=tree&filepath=test%2Frandom_search_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9yYW5kb21fc2VhcmNoX3Rlc3QuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_bessel\_j.hpp](https://app.codecov.io/gh/boostorg/math/pull/1306?src=pr&el=tree&filepath=test%2Ftest_bessel_j.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9qLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_functions\_for\_optimization.hpp](https://app.codecov.io/gh/boostorg/math/pull/1306?src=pr&el=tree&filepath=test%2Ftest_functions_for_optimization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Z1bmN0aW9uc19mb3Jfb3B0aW1pemF0aW9uLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1306?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1306?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [73d7523...4d5ec95](https://app.codecov.io/gh/boostorg/math/pull/1306?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-08-22 11:52:19 UTC  
> Url: https://github.com/boostorg/math/pull/1306#issuecomment-3214101068  

Hi folks, I would like an independent advice on this particular PR.  
  
I started out with some trivial syntax changes to remove warnings. The areas in the scope of changes included Bessel-J and Lambert-W implementation(s) and test(s).  
  
While running CI, however, some [seemingly unrelated compilation issues](https://github.com/boostorg/math/actions/runs/17149309401/job/48651710121) arose on `__APPLE__` when executing dustribution tests with `Boost.Units`. So I was drawn to [this comment](https://github.com/boostorg/math/blob/e4f1d82abe7bd70dc0f1c298448ff44d471099e5/test/test_functions_for_optimization.hpp#L13). I empirically found that the tests activated with `#define BOOST_MATH_TEST_UNITS_COMPATIBILITY 1` have (as of today-ish) also begun to fail on `__APPLE__`. Three lines later, you can see my patch/hack of `#if 0`, which deactivates these tests everywhere. CI is now running again.  
  
I was wondering if this discouraging discovery could be verified or investigated by another person? It is always good to keep maximum scope of testing. But I've come to the conclusion that minute changes on the GHA runner now cause these tests to fail also on `__APPLE__`. Can any alternate tests (that are maybe more friendly to compilers) be made with modest effort?  
  
Cc: @mborland and @NAThompson and @jzmaddock

---

## Comment 3

> Username: mborland  
> Created_at: 2025-08-22 13:39:29 UTC  
> Url: https://github.com/boostorg/math/pull/1306#issuecomment-3214419597  

I am able to replicate the error locally on my Mac. Looks like there's a static assert inside the STL now to check for a standard type being used for `uniform_real_distribution`. I think the workaround here would be to use `boost::random::uniform_real_distribution` when not in standalone mode. That should capture this use case anyway because Boost.Units does not support it's own standalone.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2025-08-22 14:19:43 UTC  
> Url: https://github.com/boostorg/math/pull/1306#issuecomment-3214544867  

I would recommend just deleting that unit test. IIRC, Boost.Units compatibility _did_ find a bug, but we can't maintain all of Boost just to maintain Boost.Math.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-08-22 14:40:01 UTC  
> Url: https://github.com/boostorg/math/pull/1306#issuecomment-3214613257  

> I would recommend just deleting that unit test.  
  
OK. Confirmed. Will do.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-08-22 19:11:15 UTC  
> Url: https://github.com/boostorg/math/pull/1306#issuecomment-3215349885  

Thanks @NAThompson and @mborland.  
  
Merged

---
