# #1086 Linear BIG_CONST macros and try lcov-excl [Closed]

> Username: ckormanyos  
> Created at: 2024-02-12 05:00:49 UTC  
> Updated at: 2024-03-02 07:25:57 UTC  
> Closed at: 2024-03-02 07:25:56 UTC  
> Url: https://github.com/boostorg/math/pull/1086  

The purpose of this PR is to streamline/linearize macros sich as `BOOST_MATH_BIG_CONSTANT` and subsequently check if line-by-line lcov-exclude makes sense.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-12 05:54:49 UTC  
> Updated_at: 2024-02-12 13:22:14 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938099283  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `8 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`61a03e0`)](https://app.codecov.io/gh/boostorg/math/commit/61a03e02034b16fd4d787a831a2bb7782cf87886?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 88.61% compared to head [(`12ee031`)](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 89.82%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1086/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1086      +/-   ##  
===========================================  
+ Coverage    88.61%   89.82%   +1.21%       
===========================================  
  Files          768      768                
  Lines        64669    63722     -947       
===========================================  
- Hits         57306    57241      -65       
+ Misses        7363     6481     -882       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/quadrature/gauss.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZ2F1c3MuaHBw) | `93.04% <ø> (ø)` | |  
| [include/boost/math/quadrature/gauss\_kronrod.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZ2F1c3Nfa3JvbnJvZC5ocHA=) | `95.45% <ø> (ø)` | |  
| [...de/boost/math/special\_functions/detail/erf\_inv.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9lcmZfaW52LmhwcA==) | `95.04% <ø> (ø)` | |  
| [...ost/math/special\_functions/detail/igamma\_large.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pZ2FtbWFfbGFyZ2UuaHBw) | `97.77% <ø> (+64.66%)` | :arrow_up: |  
| [...ost/math/special\_functions/detail/lgamma\_small.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9sZ2FtbWFfc21hbGwuaHBw) | `91.72% <ø> (+32.36%)` | :arrow_up: |  
| [include/boost/math/special\_functions/expm1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2V4cG0xLmhwcA==) | `76.92% <100.00%> (+11.32%)` | :arrow_up: |  
| [include/boost/math/special\_functions/lanczos.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2xhbmN6b3MuaHBw) | `87.86% <ø> (+50.92%)` | :arrow_up: |  
| [include/boost/math/tools/big\_constant.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2JpZ19jb25zdGFudC5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_1F1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0XzFGMS5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_gamma\_edge.cpp](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dhbW1hX2VkZ2UuY3Bw) | `100.00% <100.00%> (ø)` | |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1086/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [61a03e0...12ee031](https://app.codecov.io/gh/boostorg/math/pull/1086?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-02-12 06:00:54 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938102851  

Hi @jzmaddock and @mborland and @NAThompson this work might seem ugly/controversial/hurried. But I looked into simplifying the big-constant macros such that they can be neatly expressed as single-line macros. This allows for simple line-by-line `LCOV_EXCL_LINE` on lines that (should be) rather well-known to be covered but which `lcov` seems to be missing (maybe due to optimization settings).  
  
Anyway, it is a method, but might be controversial. Comments? Thoughts?

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-02-12 07:29:27 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938166868  

I've tried (but not yet succeeded) to directly put the code annotation into the macros themselves.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-02-12 07:51:51 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938187707  

One thing I did notice is that when the couple hundred annotations are in place, it really draws focus to the several hundred remaining specfun missed lines that are actually much more, let's say, interesting and content-rich. So feedback would be appreciated, ...  
  
Cc: @jzmaddock and @mborland  and @NAThompson

---

## Review 5 [Approved]

> Username: mborland  
> Created_at: 2024-02-12 07:55:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1086#pullrequestreview-1874677212  

Looks good to me. 200+ lines removed does help.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-02-12 07:59:40 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938195074  

> Looks good to me. 200+ lines removed does help.  
  
Thanks Matt. It ended up being closer to 1,000 lines. I could not find any other large, missed tables in specfun. But some were, in fact, covered. I annotated the ones I found must have been covered but were seemingly mistakenly marked by `lcov` as uncovered.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2024-02-12 08:40:05 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938240442  

Here are the remaining trouble spots:  
- specfun  
- distributions  
- octonion.hpp  
  
This is a really great achievement so far in Math!  
  
![grafik](https://github.com/boostorg/math/assets/2404721/e849b83c-9f5d-4c5b-98fc-0b38484f0b33)

---

## Comment 8

> Username: mborland  
> Created_at: 2024-02-12 09:05:45 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938274481  

> Here are the remaining trouble spots:  
> * octonion.hpp  
  
The one test file doesn't have real testing in it: https://github.com/boostorg/math/blob/develop/test/octonion_test.cpp.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2024-02-12 09:45:32 UTC  
> Updated_at: 2024-02-12 09:45:42 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938333859  

> The one test file doesn't have real testing in it:  
  
I suppose we might as well just exclude it from the `lcov` analysis at the command_line-level?

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2024-02-12 09:48:34 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938338408  

>> The one test file doesn't have real testing in it:  
  
> I suppose we might as well just exclude it from the `lcov` analysis at the command_line-level?  
  
On second thought, that is too hasty. This is our one-time chance to actually get some octernion testing. Or notice that they fail. I think we should try for tests. I can give this one a crack in another branch later, ...

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2024-02-12 10:14:47 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938384249  

Forgive me, late night last night... but BOOST_MATH_BIG_CONSTANT is now being called with 2 arguments, but still defined with 3?  I know I'm missing something... !  
  
BTW the basic issue is that multi-line statements are badly handled by lcov, so there are quite a few function calls that look like:  
  
```  
foo(  
   a,   
   b,  
   c);  
```  
  
especially when the arguments are long error messages... and these are marked as uncovered except for the last line.  The issue with the arrays are the same, it's not the macros as such that cause the issue, just that we have dynamic initialization spread over several lines.  For the functions I've been collapsing down to one line where possible, and using LCOV_EXCL_LINE when not.  For the arrays, I've topped and tailed the list of initializers with LCOV_EXCL_START/LCOV_EXCL_STOP.  
  
BTW I have just started on coverage for beta, hoping to get to erf or so in the next tranche, I am going to be super busy this week though unfortunately :(

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2024-02-12 10:22:33 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938397168  

Hi John, thanks for those explanations.  
  
>  but `BOOST_MATH_BIG_CONSTANT` is now being called with 2 arguments, but still defined with 3?  
  
Not exaclty, but there are such changes in this PR. That's me, messing with stuff.  
  
I left the number of parameters of `BOOST_MATH_BIG_CONSTANT`to be 3. And it is still called in the PR with 3.  
  
The other mega-macro, `BOOST_MATH_HUGE_CONSTANT` originally had 3 parameters, but the digits parameter was used nowhere. So I removed it.  
  
> For the arrays, I've topped and tailed the list of initializers with `LCOV_EXCL_START`/`LCOV_EXCL_STOP`  
  
I can do that and eliminate the thousand or so single-line annotations in this PR. I did, however, diligently look if I felt that they were covered prior to x-ing them out. So I'm good with that.  
  
Also, I kind of like the way I refactored `BOOST_MATH_BIG_CONSTANT` and `BOOST_MATH_HUGE_CONSTANT`, as I think the intermediary inline function add clarity. Sorry about silently going from 3 to 2 params on `_HUGE`, but the unused param was in my mind simply confusing.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2024-02-12 10:35:39 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938417757  

oops. I did mess up some of the calls to `BOOST_MATH_BIG_CONSTANT`. My bad. I will repair all these.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2024-02-12 11:12:23 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938474611  

> oops. I did mess up some of the calls to `BOOST_MATH_BIG_CONSTANT`. My bad. I will repair all these.  
  
I'll finish this up (whatever I started) but just let it sit there, as it seems like it was a bit _hurried_.  
  
@jzmaddock if you want some, none, or all of it, pls let me know. It was/is a good exercise.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2024-02-12 11:41:53 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1938517708  

> I'll finish this up (whatever I started) but just let it sit there, as it seems like it was a bit _hurried_.  
  
I do, in fact, like the single-line expression(s) of `BOOST_MATH_BIG_CONSTANT`/`BOOST_MATH_HUGE_CONSTANT`. This allows these to be `lcov`-excluded on single-line-annotations.  
  
The three-parameters of `_HUGE` are controversial. But if any part of this is cool, it's that work. Or any part of it.  
  
Anyway, I'll let this one _bake_ for a while.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2024-02-17 14:14:01 UTC  
> Url: https://github.com/boostorg/math/pull/1086#issuecomment-1950220295  

I converted this PR to a draft. I'm not going to really dive into this any time soon, but I synchronized with Develop and it now shows some of the tabble/macro work that subjectively seemed to make sense to me.

---
