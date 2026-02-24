# #1069 Improve test coverage. [Merged]

> Username: jzmaddock  
> Created at: 2024-01-21 16:58:35 UTC  
> Updated at: 2024-02-11 12:12:26 UTC  
> Merged at: 2024-02-10 16:47:45 UTC  
> Closed at: 2024-02-10 16:47:45 UTC  
> Url: https://github.com/boostorg/math/pull/1069  

Try and get all the special_functions/a*.hpp files up to 100%.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-01-21 16:59:22 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1902695482  

Refs https://github.com/boostorg/math/issues/1068

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-01-21 17:55:21 UTC  
> Updated_at: 2024-01-24 18:23:43 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1902709954  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`e06b1b7`)](https://app.codecov.io/gh/boostorg/math/commit/e06b1b7b04b90f73d0fb090d2740b52c27784d35?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.24% compared to head [(`045a87e`)](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 88.32%.  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1069/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1069      +/-   ##  
===========================================  
+ Coverage    85.24%   88.32%   +3.07%       
===========================================  
  Files          875      762     -113       
  Lines        66568    64394    -2174       
===========================================  
+ Hits         56745    56875     +130       
+ Misses        9823     7519    -2304       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/acosh.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2Fjb3NoLmhwcA==) | `100.00% <100.00%> (+10.00%)` | :arrow_up: |  
| [include/boost/math/special\_functions/airy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2FpcnkuaHBw) | `100.00% <ø> (+1.40%)` | :arrow_up: |  
| [include/boost/math/special\_functions/asinh.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2FzaW5oLmhwcA==) | `100.00% <100.00%> (+8.69%)` | :arrow_up: |  
| [include/boost/math/special\_functions/atanh.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2F0YW5oLmhwcA==) | `100.00% <100.00%> (+13.79%)` | :arrow_up: |  
| [include/boost/math/special\_functions/bernoulli.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2Jlcm5vdWxsaS5ocHA=) | `100.00% <ø> (+13.33%)` | :arrow_up: |  
| [include/boost/math/special\_functions/bessel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2Jlc3NlbC5ocHA=) | `100.00% <100.00%> (+16.66%)` | :arrow_up: |  
| [...lude/boost/math/special\_functions/bessel\_prime.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2Jlc3NlbF9wcmltZS5ocHA=) | `98.92% <100.00%> (+14.61%)` | :arrow_up: |  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `96.10% <ø> (+21.29%)` | :arrow_up: |  
| [.../boost/math/special\_functions/detail/bessel\_i0.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfaTAuaHBw) | `84.61% <ø> (+71.51%)` | :arrow_up: |  
| [test/acosh\_test.hpp](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9hY29zaF90ZXN0LmhwcA==) | `72.50% <100.00%> (+3.05%)` | :arrow_up: |  
| ... and [14 more](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [203 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1069/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e06b1b7...045a87e](https://app.codecov.io/gh/boostorg/math/pull/1069?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-02-09 19:36:53 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1936496077  

Dude it's massive +3 persent in one swath!

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-02-10 15:41:48 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937046812  

OK folks, I hope this tranche of coverage improvement is good to go, I'll start a fresh PR for any more before the changes here get out of hand.  @NAThompson there are a couple of drone failures in random_search_test which appear unrelated - tolerance too tight, or something non-deterministic in there?

---

## Comment 5

> Username: NAThompson  
> Created_at: 2024-02-10 16:20:31 UTC  
> Updated_at: 2024-02-10 16:21:26 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937054922  

@jzmaddock : I noticed that yesterday and provided what I hoped was a fix; looks like I didn't quite get it.  
  
I recommend merging over the failure and I'll hopefully figure this one out pretty quick . . .

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-02-10 16:48:30 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937060684  

> merging over the failure and I'll hopefully figure this one out pretty quick . . .  
  
done. merged. Thank you John.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2024-02-10 16:49:06 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937060816  

Ughhhh wait a minute. I just merged your branch. Not mine.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-02-10 16:49:45 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937060935  

I guess I need to revert that commit? I hope I did not turn this into a mess.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-02-10 16:50:34 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937061115  

> Ughhhh wait a minute. I just merged your branch. Not mine.  
  
Should be OK ;)  
  
> I recommend merging over the failure and I'll hopefully figure this one out pretty quick . . .  
  
OK lets see what happens on develop

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2024-02-10 16:51:34 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937061310  

> I guess I need to revert that commit? I hope I did not turn this into a mess.  
  
Should be OK unless someone notices any SNAFU's.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2024-02-10 16:51:53 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937061363  

Indeed. I would just end up fumbling the fumble. I will merge my dos to develop.  
  
Then we just have the remaining very few test failures.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2024-02-10 18:15:38 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937087124  

> Should be OK unless someone notices any SNAFU's.  
  
Thanky you John! Probably, with your expert help (as usual) we can dig out of this.  
  
OK, so finally, I merged the new docs. This contains all your new coverage stuff. The docs look good, but the very few test failures are still present.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2024-02-11 12:12:25 UTC  
> Url: https://github.com/boostorg/math/pull/1069#issuecomment-1937662462  

>> Should be OK   
  
> Thanky you John!  
  
As far as I can tell, everything is now OK on develop. @NAThompson triggered a few runs in his new work in #1083, and drone, GHA are OK there. Thanks again @jzmaddock, and sorry about the back-and-forth.

---
