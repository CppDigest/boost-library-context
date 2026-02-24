# #674 Handle implicit fallthrough warn on no-except [Merged]

> Username: ckormanyos  
> Created at: 2025-04-08 13:26:32 UTC  
> Updated at: 2025-04-11 06:04:25 UTC  
> Merged at: 2025-04-11 06:04:17 UTC  
> Closed at: 2025-04-11 06:04:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-04-08 13:26:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2786438468  

See #673

---

## Comment 2

> Username: mborland  
> Created_at: 2025-04-08 13:40:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2786480893  

I guess it's time to upgrade the CI system too. Ubuntu Focal will be removed by Github next week...

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-04-08 13:45:59 UTC  
> Updated_at: 2025-04-08 13:46:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2786496916  

> I guess it's time to upgrade the CI system too. Ubuntu Focal will be removed by Github next week  
  
Fair enough. Thanks @mborland. What do you think we should do with all those tests? Should we put them on ubuntu-latest. or 22.04? Should we simply drop them?  
  
Cc: @jzmaddock

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-04-08 13:51:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2786513591  

> What do you think we should do with all those tests? Should we put them on ubuntu-latest. or 22.04? Should we simply drop them?  
  
Intuitively, I would bring all the focal tests onto 24.04 (ubuntu-latest) and run them with GCC 13.  
  
Thoughts?

---

## Comment 5

> Username: mborland  
> Created_at: 2025-04-08 13:57:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2786532451  

I've been putting all the new libs on runners copied and pasted from the Boostorg/boost-ci repo. These run latest as the host and everything else in containers.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2025-04-08 14:01:07 UTC  
> Updated_at: 2025-04-11 06:04:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2786545587  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`d42c7a8`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d42c7a878e1cde2fd85a193af4dbf3db9f491de9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`cdbffdc`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/cdbffdc0ece737212a66abf25d8eb6039651a270?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/674/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #674   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          279     279             
  Lines        28983   28983             
=======================================  
  Hits         27257   27257             
  Misses        1726    1726             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/gauss\_laguerre\_quadrature.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?src=pr&el=tree&filepath=example%2Fgauss_laguerre_quadrature.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9nYXVzc19sYWd1ZXJyZV9xdWFkcmF0dXJlLmNwcA==) | `90.7% <ø> (ø)` | |  
| [include/boost/multiprecision/cpp\_bin\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0LmhwcA==) | `89.7% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d42c7a8...cdbffdc](https://app.codecov.io/gh/boostorg/multiprecision/pull/674?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2025-04-08 16:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2786990040  

The fix is fine, but I have a hunch will generate "unreachable code" warnings when building with exceptions ON.  Maybe put those lines inside a #if block?  Annoying I know....

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2025-04-08 17:06:32 UTC  
> Updated_at: 2025-04-08 17:06:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2787124585  

> The fix is fine, but I have a hunch will generate "unreachable code" warnings when building with exceptions ON.   
  
Yep, goti it. I will be super-sure to decorate with the PP so that no corollary warnings arise. Nice point John (@jzmaddock).  
  
Will hit this after the CI blitz subsides...

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2025-04-10 18:43:26 UTC  
> Updated_at: 2025-04-10 18:46:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2794824379  

> I guess it's time to upgrade the CI system too. Ubuntu Focal will be removed by Github next week...  
  
See also #675 upstream modernization of GHA  
  
Many thanks @mborland

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2025-04-10 20:33:23 UTC  
> Updated_at: 2025-04-10 20:33:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2795098719  

Drone is way too long.  Whatever on earth is running there, we need to reduce the scope of testing on that thing. I'm looking for half an hour or less. This multi-hour thing isn't how it's supposed to work.  
  
I'll try to figure out what's taking forever there, and reduce the cycles. But that'll be after this PR.  
  
Cc: @mborland and @jzmaddock

---

## Comment 11

> Username: mborland  
> Created_at: 2025-04-10 20:35:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2795103704  

It looks like you're waiting on s390x runners. They are in pretty limited supply, but generally available. 26 mins is the longest single runner that's complete. Let me ping the guy that manages drone.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2025-04-10 20:43:48 UTC  
> Updated_at: 2025-04-10 20:44:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2795119955  

> It looks like you're waiting on s390x runners. They are in pretty limited supply, but generally available. 26 mins is the longest single runner that's complete. Let me ping the guy that manages drone.  
  
Ah thanks Matt (@mborland) I hadn't thought of that exactly. Are you waiting for run-time to trickle through, ...  
Or are you wainting for a runner to even start.  
  
Either way, it's a lag-time that quite honestly needs to be critically examined and weighed regarding added value or lack thereof WRT costs and time.

---

## Comment 13

> Username: mborland  
> Created_at: 2025-04-10 20:45:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2795123244  

Waiting for the runner to even start. I'm willing to bet it's related to release going out today since every boost library is showing recently updated. Basically DDoSed drone.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2025-04-10 20:51:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/674#issuecomment-2795138871  

> Waiting for the runner to even start. I'm willing to bet it's related to release going out today since every boost library is showing recently updated. Basically DDoSed drone.  
  
10-4.  
I will chill and check it out.

---
