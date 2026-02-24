# #425 Add g++-5 to ci [Merged]

> Username: ckormanyos  
> Created at: 2022-01-31 17:09:34 UTC  
> Updated at: 2022-02-03 05:32:12 UTC  
> Merged at: 2022-02-02 21:09:11 UTC  
> Closed at: 2022-02-02 21:09:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-01-31 17:14:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1026013413  

This first naive try to scope out GCC 5 on bionic may not work because not all the standards of the matrix are available.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-01-31 17:16:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1026015058  

The first naive try is probably destined to fail since GCC 5 does supports neither `-std=c++14` nor `-std=c++17` in the test matrix.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-02-01 10:23:59 UTC  
> Updated_at: 2022-02-01 10:25:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1026687589  

So this ran surprisingly well --- not perfect, but well. I think all the dedicated, consistent work on going standalone really paid off.   
  
So... anyway, In a dedicated run, I added the basic Multiprecision g++-5 tests as shown [here](https://github.com/boostorg/multiprecision/blob/0b57300cbd43c5e8a918eefe56f7ba98993af6e8/.github/workflows/multiprecision.yml#L400).  
  
I had expected quite a few failures, but there was one single test failure in `cpp_int_tests` regarding a [floating-point-exception](https://github.com/boostorg/multiprecision/runs/5009806730?check_suite_focus=true#step:17:115). I can probably track down the root cause locally.  
  
I don't know how to guage the excitement on getting g++-5 back running in CI. This can definitely wait for 1.80 (post 1.79), since there is a lot going on.  
  
But it does, in fact, seem feasible to go for g++-5.  
  
Thoughts?  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-02-01 19:07:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1027189825  

If it's straightforward then by all means go for it.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-02-01 19:19:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1027200295  

> If it's straightforward then by all means go for it.  
  
While underway, I found a TODO for C++11.  
  
I took a try at reworking the `timer` facility based on a TODO note I encountered. I opted for a C++11 design which results in both a `stopwatch` facility as well as the original interface to a class called `timer`. Both of these may be useful.  
  
I am a bit unsure of the new design I made.  
  
If anyone gets a chance (no hurry though), ... could you please taka a look at [this](https://github.com/boostorg/multiprecision/blob/try_to_get_gcc5_back_in_ci/test/timer.hpp)? (Because `<chrono>` tends to be a bit of a wily one somethines...).  
  
Cc: @jzmaddock and @mborland

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2022-02-02 11:06:48 UTC  
> Updated_at: 2022-02-02 11:07:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1027827765  

I think I found something. This might turn into a longer story. So I'll get this communication started. If I'm wrong please correct this.  
  
I found that [test_cpp_rational.cpp](https://github.com/boostorg/multiprecision/blob/develop/test/test_cpp_rational.cpp) was generally running slowly (on PC locally) and also crashing in CI on g++-5 in GHA.  
  
So I found [this line](https://github.com/boostorg/multiprecision/blob/6e22c23e0bf551deeb01b6fd250e6107201ceb99/test/test_cpp_rational.cpp#L1178). The `numeric_limits::digits` will give zero when compiling with `__GNUC__` and using `-std=c++11` (or `14`, `17`, etc.). So the number of random digits requested underflows and explodes.  
  
Please note GHA tests are using something like `-std=c++11` and not a GNU extension.  
  
So in this PR, I have:  
- Repaired [test_cpp_rational.cpp](https://github.com/boostorg/multiprecision/blob/develop/test/test_cpp_rational.cpp). Use `<random>` and generally `C++11`-ify it.  
- Completely reworked the `timer` facility to be `C++11`-ified.  
- Add `g++-5` tests to CI's GHA jobs.  
  
I am not getting through the traffic jamon CI.  
  
But I believe the underflow on _digits_ I mention above might actually be a real factor that we might want to address for 1.79 itself.  
  
@jzmaddock and @mborland please do comment on this or correct my thinking if I'm off on a tangent here...  
  
Many thanks, guys.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2022-02-02 12:03:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1027871173  

> So I found this line. The numeric_limits::digits will give zero when compiling with __GNUC__ and using -std=c++11 (or 14, 17, etc.). So the number of random digits requested underflows and explodes.  
  
Ah :(  Yes, double_limb_type is __int128 and numeric_limits is not specialized unless you compile with --gnu++XX, I'll check over the diff shortly, this is an easy one to get wrong and mess up as you've just discovered! :(  
  
Good catch that!  If Github had a big thumbs up emoji imagine it here :)

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-02-02 12:09:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1027874847  

Diff looks good to me.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2022-02-02 17:36:16 UTC  
> Updated_at: 2022-02-02 17:36:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1028184603  

> Diff looks good to me.  
  
Thanks John (@jzmaddock)  
  
CI on GHA is moving in the green direction. There was a hicup on drone.  
  
I could `[ci skip]` a trivial commit to restart it or (when/if) GHA runs green. Or I could also simply merge to develop. But before deciding that, ... Could I get a second opinion on [this](https://drone.cpp.al/boostorg/multiprecision/445).  
  
To me, it looks like spurious runner problems on drone. What's your take?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2022-02-02 18:18:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1028223237  

Yep spurious: couldn't connect to ubuntu.com for some reason.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2022-02-02 18:51:03 UTC  
> Updated_at: 2022-02-02 18:51:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1028251534  

> Yep spurious: couldn't connect to ubuntu.com for some reason.  
  
Thanks John. So when I merge this, Multiprecision will officially support GCC5 on `-std=c++11`and `-std=c++14`. I activated a subset of the key tests (but the most important ones) on CI. So I think it's fair to say, after this PR, we have GCC5 again.  
  
This will get in for 1.79.  
  
I do have interest in finding out how it looks for 4.8. But i will not press or force this issue in February. I'll take a cursory glance, however, in a future PR and we can see if it's feasible.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2022-02-02 21:06:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/425#issuecomment-1028357821  

Failures in CI are:  
- Spurious failure of drone  
- Two server communication errors in GHA  
  
Merging to develop

---
