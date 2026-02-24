# #1094 More coverage of instantiation [Merged]

> Username: ckormanyos  
> Created at: 2024-02-15 03:46:31 UTC  
> Updated at: 2024-02-15 10:43:03 UTC  
> Merged at: 2024-02-15 10:42:59 UTC  
> Closed at: 2024-02-15 10:42:59 UTC  
> Url: https://github.com/boostorg/math/pull/1094  

The purpose of this PR is to cover most of the remaining [`instantiate.hpp`](https://github.com/boostorg/math/blob/develop/test/compile_test/instantiate.hpp).  
  
As a first step, however, remove `BOOST_MATH_NO_DISTRIBUTION_CONCEPT_TESTS` and let's see if instantiation sinply now compiles and runs in standalone (it was previously excluded from standalone).

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-02-15 04:15:39 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945335620  

As it turns out, standalone can not tolerate distribution concept checks (due to necessity of `function_requires`). But standalone can (probably) handle all the instantiations/runs of the special functions. Special function instantiations were previously excluded from standalone (along for the ride) with distribution concept checks.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-02-15 06:37:39 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945454075  

Hi @mborland I'm a bit confused by some failures in standalone CI-block 1 (where this PR now attempts to run more concept checks on special functions).  
  
I get some errors on exception handling in hypergeometric. Also I see something like missing threads in Bernoulli, claiming I need `BOOST_MATH_BERNOULLI_UNTHREADED`. I thought I could use most special functions in standalone.  
  
The file with new features is [`instantiate.hpp`](https://github.com/boostorg/math/blob/cover_instantiate_more/test/compile_test/instantiate.hpp). The big difference in this PR is that _only_ distribution concept checks are disabled whereas special function concept checks are active. I think we are close.  
  
Can you see anything obvious I need to configure? I did not know if I needed specialties for standalone that I maybe forgot?

---

## Comment 3

> Username: mborland  
> Created_at: 2024-02-15 07:13:44 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945486332  

>   
> Can you see anything obvious I need to configure? I did not know if I needed specialties for standalone that I maybe forgot?  
  
Your actual failures are coming from: https://github.com/boostorg/math/actions/runs/7910989740/job/21594474668?pr=1094#step:17:1016. I didn't disable exception handling in the standalone testing but there may be a colliding configuration somewhere else? The Bernoulli thing is marked as failed as expected which is fine.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-02-15 08:21:16 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945574439  

Hi @mborland it perplexes me. In the Jam file there are places around lines 505-506 that compile test_instantiate numbers 1 and 2. This is done both with and without exceptions. And it has worked for a long time and still does in my branch for non-standalone.  
  
In the failures we get now these are from the latter line 506 which disables exceptions via command line argument to the compiler. And it is here that it fails for standalone.  
  
I’m not super experienced with standalone but I don’t see why I shouldn’t be able to disable exceptions on command line and fail compiling certain special functions. Sure I could throw in BOOST_DISABLE_EXCEPTIONS for the standalone case, but I don’t think I should have to?

---

## Comment 5

> Username: mborland  
> Created_at: 2024-02-15 08:36:18 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945596570  

> Hi @mborland it perplexes me. In the Jam file there are places around lines 505-506 that compile test_instantiate numbers 1 and 2. This is done both with and without exceptions. And it has worked for a long time and still does in my branch for non-standalone.  
>   
> In the failures we get now these are from the latter line 506 which disables exceptions via command line argument to the compiler. And it is here that it fails for standalone.  
>   
> I’m not super experienced with standalone but I don’t see why I shouldn’t be able to disable exceptions on command line and fail compiling certain special functions. Sure I could throw in BOOST_DISABLE_EXCEPTIONS for the standalone case, but I don’t think I should have to?  
  
I think something that was lost is non-standalone mode boost.throw_exception was dealing with the no-except flag whereas in standalone it wasn't. I added a commit that should handle this now.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-02-15 09:21:13 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945675670  

Great find @mborland I’ll let this green up. Then I’ll activate the remaining 500 uncovered lines. I’ll then let all the instantiation stuff in all test suites then not only compile but also run. Should be done in a couple days.

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2024-02-15 09:34:15 UTC  
> Updated_at: 2024-02-15 10:20:33 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945696985  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`593fea8`)](https://app.codecov.io/gh/boostorg/math/commit/593fea8f4f7a0fa4476e2ba607fd5aa8311d685c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 91.80% compared to head [(`9cbb361`)](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 91.80%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1094/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1094   +/-   ##  
========================================  
  Coverage    91.80%   91.80%             
========================================  
  Files          769      769             
  Lines        63833    63833             
========================================  
  Hits         58599    58599             
  Misses        5234     5234             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/compile\_test/instantiate.hpp](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb21waWxlX3Rlc3QvaW5zdGFudGlhdGUuaHBw) | `64.52% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [593fea8...9cbb361](https://app.codecov.io/gh/boostorg/math/pull/1094?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-02-15 10:42:49 UTC  
> Url: https://github.com/boostorg/math/pull/1094#issuecomment-1945816968  

This is actually a good intermediate result with an actual configuration improvement. For the sake of clarity, I’ll merge this one now.  
  
then I’ll cover the remaining mixed instantiation lines in a soon-to-come future PR  
  
cc: @mborland and @jzmaddock and @NAThompson

---
