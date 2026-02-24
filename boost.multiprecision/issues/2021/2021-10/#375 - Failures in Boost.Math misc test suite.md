# #375 - Failures in Boost.Math misc test suite [Closed]

> Username: mborland  
> Created at: 2021-10-06 17:35:34 UTC  
> Updated at: 2021-11-02 19:11:49 UTC  
> Closed at: 2021-11-02 18:55:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375  

The quaternion tests are now failing using `cpp_dec_float` ([see here](https://github.com/boostorg/math/runs/3806626840?check_suite_focus=true#step:18:1251)). Looks to be an inexact comparison between a floating point type and an integer.  
  
Bernoulli tests also now fail using `cpp_bin_float` as it registers as an unsupported configuration ([see here](https://github.com/boostorg/math/runs/3806626840?check_suite_focus=true#step:18:1296)).

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-10-06 18:24:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-936846904  

> quaternion tests are now failing using `cpp_dec_float` ... Looks to be an inexact comparison between a floating point...  
  
Hi @mborland thenaks for finding this Matt!  
  
Well, ... the fact that `cpp_dec_float` does not round has been present since the inception of `cpp_dec_float`.  
  
Although this is not particularly _nice_, this behavior has not changed recently (although there is a completely separate, unmerged issue/branch related to this).  
  
So I wonder what makes the difference in these tests now? Is it more exact knowledge or behavior of certain limbs when dealing with them within a `constexpr` context? Do we/I need to fundamentally change anything in `cpp_dec_float`? Or can we deal with that particular collection of test cases via tolerance increases?  
  
Cc: @jzmaddock

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-07 08:07:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-937554112  

> So I wonder what makes the difference in these tests now? Is it more exact knowledge or behavior of certain limbs when dealing with them within a constexpr context? Do we/I need to fundamentally change anything in cpp_dec_float? Or can we deal with that particular collection of test cases via tolerance increases?  
  
I don't have time to debug this right now, but the failing tests really should have exact integer results IMO.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-10-07 08:11:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-937557514  

>Bernoulli tests also now fail using cpp_bin_float as it registers as an unsupported configuration (see here).  
  
That's weird and rather perplexing, either I've made a mistake reconfiguring the Bernoulli code (but this *did* all pass I'm sure), or there's something up with <atomic>.  I might need to add an extra *info.cpp program to Boost.Config to probe the contents of <atomic> so we can see what's up.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-10-07 08:53:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-937589346  

>>  what makes the difference in these tests now?  
  
> the failing tests really should have exact integer results IMO  
  
Yes. I will try to reproduce the behavior and go forward with debug.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-10-07 17:58:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-938026129  

Aaaahh, the Bernoulli failure isn't a failure - it's a test that is expected not to compile and the actual test does pass.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-11-02 17:57:24 UTC  
> Updated at: 2021-11-02 17:57:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-957993071  

Hi @mborland I believe @jzmaddock handled this by disabling a handful of `cpp_dec_float` tests in Boost.Math's `misc`suite. See also [math #712](https://github.com/boostorg/math/pull/712)  
  
  
Can we close?

---

## Comment 7

> Username: NAThompson  
> Created at: 2021-11-02 18:06:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-958000121  

> Aaaahh, the Bernoulli failure isn't a failure - it's a test that is expected not to compile and the actual test does pass.  
  
I got burned by this one as well. Is there a way just to not print anything on expected failures?

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-11-02 18:55:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-958060333  

Yes!

---

## Comment 9

> Username: jzmaddock  
> Created at: 2021-11-02 18:59:58 UTC  
> Updated at: 2021-11-02 19:00:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-958070783  

>I got burned by this one as well. Is there a way just to not print anything on expected failures?  
  
Not that I'm aware of.  The trick when searching for failures is to always search for "..failed" or "\\.\\.failed" if this is a regex (grep etc).

---

## Comment 10

> Username: NAThompson  
> Created at: 2021-11-02 19:11:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/375#issuecomment-958090435  

Ah I see. I always grepped for `error`.
