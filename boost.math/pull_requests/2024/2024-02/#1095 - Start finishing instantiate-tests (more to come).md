# #1095 Start finishing instantiate-tests (more to come) [Merged]

> Username: ckormanyos  
> Created at: 2024-02-15 17:24:36 UTC  
> Updated at: 2024-02-16 17:44:31 UTC  
> Merged at: 2024-02-16 17:44:12 UTC  
> Closed at: 2024-02-16 17:44:12 UTC  
> Url: https://github.com/boostorg/math/pull/1095  

The purpose of this PR is to let many instantiation tests also run. Standalone/non-Standalone symmetry is achieved. Time to finish this and cover the last 500 lines in `mixed`-whatever.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-02-15 17:26:33 UTC  
> Updated_at: 2024-02-15 17:27:22 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1946678420  

First step does not include the full 500 missing lines. These will be present in run-time shortly.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-02-15 18:22:06 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1946857365  

Hi John (@jzmaddock) How about this preliminary fix on 1 of the seven places to fix that domain assertion. Such powerful language, ... _domain_ _assertion_.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-02-15 18:22:49 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1946859595  

Let's see if it cycles green...

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 18:35:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1095#pullrequestreview-1883546689  

📁 test/test_compile_test_and_run.cpp

```diff
   8 |+ 
   9 | #include <boost/core/lightweight_test.hpp>
  10 | #include <boost/cstdfloat.hpp>
```

> Username: jzmaddock  
> Created_at: 2024-02-15 18:35:17 UTC  
> Updated_at: 2024-02-15 18:35:18 UTC  
> Url: https://github.com/boostorg/math/pull/1095#discussion_r1491448910  

I have a hunch that cstdfloat.hpp is pulling in something that sets the policy defaults before instantiate.hpp gets included.

> Username: ckormanyos  
> Created_at: 2024-02-15 18:43:16 UTC  
> Url: https://github.com/boostorg/math/pull/1095#discussion_r1491459194  

Ok. We are moving fast in two threads, that is probably because I started two threads. So be it.   
  
We do, nonetheless, need to fix this properly. I've seen also your comments on style in the other thread. We can combine all these. But for some reason, I can't find the right syntax on the policies to make every compiler _happy_.


---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-02-15 19:10:06 UTC  
> Updated_at: 2024-02-15 19:11:09 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1947010526  

If we look [here](https://github.com/boostorg/math/actions/runs/7914656922) the code is compiling and running.  
  
And (you might call me odd), but I want that code both compiling and running. This is not usually considered strange. That's why someone wrote the code (well it was originally compile-checks, but I'm still saying it should run). So I'd like to find a way to get both.  
  
If that proves impossible, or I'm pressing too hard or fast, we can back off totally on this. But it will be hard to convince me that the code can't compile and run.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-02-16 05:19:32 UTC  
> Updated_at: 2024-02-16 05:20:00 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1947775409  

Hi John (@jzmaddock),  
  
Thanks so much for your inputs on this PR.  
  
I thought a lot about your comments. And some (but not all) of the stuff I implemented does not make sense. So what I did in this recent push was handle most of your review comments and reduce the one single mystery I had remaining (hopefully) to one single new file.  
  
The name of the original test file I had selected was somewhat non-intuitive and the new renamed file is called [`test_instantiate3.cpp`](https://github.com/boostorg/math/blob/finish_instantiate/test/test_instantiate3.cpp).  
  
It's cycling now. The `static_assert`s are back in place in the Cauchy and non-central-beta distributions.  
  
If we find a mystery I'd like to solve it. If you would strongly prefer to _stop_ attempting to execute the instantiation code, that's as easy as two single lines, ... `LCOV_EXCL_START`/`LCOV_EXCL_STOP`.  
  
But I'd like to see if the mystery compile failures are still present and find out (if so) why?  
  
Cc: @mborland and @NAThompson

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2024-02-16 06:08:50 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1947812934  

Hi @jzmaddock regardless of whether we run or don't run the code in instantiate.hpp, I have a mystery which simply confuses me.  
  
In the most recent drone run all the time `test_instantiate1.cpp` and `test_instantiate2.cpp` pass. But `test_instantiate3.cpp`, even though it seems very similar to me fails all over the place on older GCC compilers.  
  
If we can clear up the why? on this, then we can negotiate on the question, ... to run or not to run, ... the code in instantiate.hpp.  
  
Do you see any clear reason why the code in [`test_instantiate3.cpp`](https://github.com/boostorg/math/blob/finish_instantiate/test/test_instantiate3.cpp) should fail _compilation_? This has perplexed and stymied me for days.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-02-16 06:41:37 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1947838979  

The perplexing part is in [drone 1769](https://drone.cpp.al/boostorg/math/1769).  
  
Concern was earlier raised that `<boost/cstdfloat.hpp>` which has a lot of depth might be bringing in some stuff to occlude.  
  
I'll now try a run that avoids using `<boost/cstdfloat.hpp>`.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2024-02-16 07:17:23 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1947870300  

> try a run that avoids using `<boost/cstdfloat.hpp>`  
  
Well, I'm off to hit this day, but it seems like drone is cycling green without `<boost/cstdfloat.hpp>`. Now I know where to look.  
  
@jzmaddock to run or not to run code in instantiate.hpp, that is the last open question.

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2024-02-16 07:38:01 UTC  
> Updated_at: 2024-02-16 12:06:25 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1947891483  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`d4a42f9`)](https://app.codecov.io/gh/boostorg/math/commit/d4a42f930e92cc2ad4ca0d99d323f1159493c4ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 91.82% compared to head [(`bda3a02`)](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 91.88%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1095/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1095      +/-   ##  
===========================================  
+ Coverage    91.82%   91.88%   +0.05%       
===========================================  
  Files          769      769                
  Lines        63833    63831       -2       
===========================================  
+ Hits         58615    58649      +34       
+ Misses        5218     5182      -36       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/cauchy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvY2F1Y2h5LmhwcA==) | `82.50% <ø> (ø)` | |  
| [...t/math/distributions/detail/hypergeometric\_pdf.hpp](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2h5cGVyZ2VvbWV0cmljX3BkZi5ocHA=) | `96.64% <100.00%> (-0.06%)` | :arrow_down: |  
| [...lude/boost/math/distributions/non\_central\_beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfYmV0YS5ocHA=) | `91.75% <ø> (+0.04%)` | :arrow_up: |  
| [.../boost/math/special\_functions/detail/bessel\_y0.hpp](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfeTAuaHBw) | `100.00% <ø> (ø)` | |  
| [test/compile\_test/instantiate.hpp](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb21waWxlX3Rlc3QvaW5zdGFudGlhdGUuaHBw) | `66.75% <ø> (+2.22%)` | :arrow_up: |  
| [test/test\_instantiate3.cpp](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2luc3RhbnRpYXRlMy5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1095/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d4a42f9...bda3a02](https://app.codecov.io/gh/boostorg/math/pull/1095?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2024-02-16 08:07:13 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1947924895  

So it seems like <boost/cstdfloat.hpp> introduces something at compile time that occludes a default policy template parameter. What a cool search topic. I'll start searching

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2024-02-16 10:10:17 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1948094290  

@ckormanyos you need to remember that the default is a global thing, once it's been set you can't re-set it to something else.  So once policy.hpp has been #included, that's it, the defaults are set, and nothing will change that.  So either the #define needs to be at the top of the .cpp file before any #includes, or if you're relying on instantiate.hpp to set the defaults it needs, then that header needs to be the **first** #include in the list.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2024-02-16 10:25:20 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1948118492  

Thanks john. This is great. I’ll try that and see.  
  
In your honest opinion, how tough is the tussle going to be to run or not actually execute that code?

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2024-02-16 17:04:39 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1948897494  

> In your honest opinion, how tough is the tussle going to be to run or not actually execute that code?  
  
It shouldn't really be that hard, the question is whether we learn that much from it - as I said, the code was only designed as a concept check - it doesn't verify behaviour or check that the function calls do anything meaningful, those tests are elsewhere.  As I said, I don't mind it being run at all: the one caveat being that we don't accidentally fool ourselves into thinking that something is properly covered, when in fact it's really not at all.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2024-02-16 17:30:10 UTC  
> Updated_at: 2024-02-16 17:34:10 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1948952406  

>> In your honest opinion, how tough is the tussle going to be to run or not actually execute that code?  
  
> It shouldn't really be that hard, the question is whether we learn that much from it - as I said, the code was only designed as a concept check - it doesn't verify behaviour or check that the function calls do anything meaningful, those tests are elsewhere.  
  
First off I apologize if my introduction of this was awkward or my wording unfortunately stark. That was not so cool.  
  
Thanks for going on with this.  
  
I ended up going back-and-forth (and back-and-forth) twice, two entire cycles on this in my mind.  
  
My result: I really like these as unit tests. These lines were written with great attention to detail. When I first made them runable, I ran into exceptions, stack problems and a whole host of other things. This is a true gem where hundreds of special functions are called back-to-back, providing a wonderful top-level sanity check (in great addition to the instantiation check).  
  
My wish:  
  
- Address the remaining review issues (such as `scoped_ptr`).  
- Cover the `_mixed` lines.  
- And party with it.  
  
I really appreciate the instantiations as a unit test.  
  
Having said that, this post brought me into other issues.  
- `<boost/cstdfloat.hpp>` has a few simple simplifications possible. We talked in the past regarding this, but the complex template pulls in `boost/math/constants/constants.hpp`, which, today, I would not have in the top-level of boost just for $\pi$ and ${\log}(10)$.  
- We did find a configuration weakness for STANDALONE. This is good. So now standalone tests _instantiate_.  
- I'd like _even_ MORE instantiations. Bessel and Airy zeros, and a few other missed new things.  
- I wish the distribution concept checks also would run something, but I won't _press_ _my_ _luck_, ...  
  
I'm going to go on with this and try for 1.85.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2024-02-16 17:44:08 UTC  
> Updated_at: 2024-02-16 17:44:31 UTC  
> Url: https://github.com/boostorg/math/pull/1095#issuecomment-1948972369  

This thing, being now better, is actually coming in now.  
  
The sense is that it now does _not_ change the distributions in any way and we can include STANDALONE tests. So I'll chase down the review issues and `_mixed` in a future PR. But I have to jam this one in since the distributions were, in fact, a bit unjudiciously modified in develop. And that was not right.

---
