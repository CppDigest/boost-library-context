# #676 Uniform logic has or not-has GCD/LCM [Merged]

> Username: ckormanyos  
> Created at: 2025-04-15 10:16:47 UTC  
> Updated at: 2025-05-05 19:56:35 UTC  
> Merged at: 2025-05-05 19:56:24 UTC  
> Closed at: 2025-05-05 19:56:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676  

In [cpp_int/misc.hpp](https://github.com/boostorg/multiprecision/blob/ce635570f667266864c0f18d1d9accd7b591486a/include/boost/multiprecision/cpp_int/misc.hpp#L29), there is a dependency on `Boost.Integer`.  
  
But if and only if it is not standalone. Yet the standalone version does not need integer. I was wondering if it is possible to remove this dependency in the non-standalone mode? Thereby the behavior would be the same for `constexpr_gcd` in standalone as well as non-standalone mode.  
  
Why? I got some pesky warnings coming in from `Boost.Integer`.  
  
The header has also been refactored to treat having/not-having `std::gcd` in a uniform fashion with respect to compiler switches. The compiler switches were not the same in the two places earlier.  
  
Cc: @jzmaddock and @mborland

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2025-04-15 11:25:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/676#pullrequestreview-2767891451  

📁 include/boost/multiprecision/cpp_int/misc.hpp

```diff
  29 |- #include <boost/integer/common_factor_rt.hpp>
  30 |- #endif
  32 |+ #if (defined(__cpp_lib_gcd_lcm) && (__cpp_lib_gcd_lcm >= 201606L)) && (!defined(BOOST_HAS_INT128) || !defined(__STRICT_ANSI__))
```

> Username: jzmaddock  
> Created_at: 2025-04-15 11:25:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#discussion_r2044303181  

I wonder if we really need the `&& (!defined(BOOST_HAS_INT128) || !defined(__STRICT_ANSI__))` part?  Ah wait... no std::gcd and std::lcm for __int128?  I know we have our own gcd/lcm as a fall-back of last resort, but they're not actually very performant (I suspect anyway) compared to the full Boost versions.  Or perhaps I'm wrong on that?  
  
In any case we need to get these CI tests fixed up before we can do much of anything... :(


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-04-15 12:47:38 UTC  
> Updated_at: 2025-05-05 19:56:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2804939451  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/676?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`0acb5b4`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/0acb5b4aac63ef266fbe94549bfbc5f463695f6e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`83133b7`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/83133b754cff09b7a1ad5dfd6a356af43cd2f6ae?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/676/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/676?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #676     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          280     280               
  Lines        29067   29074      +7       
=========================================  
+ Hits         27340   27347      +7       
  Misses        1727    1727               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/676?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/misc.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/676?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fmisc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L21pc2MuaHBw) | `91.9% <ø> (+0.2%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/676?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/676?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0acb5b4...83133b7](https://app.codecov.io/gh/boostorg/multiprecision/pull/676?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-04-15 12:53:59 UTC  
> Updated_at: 2025-04-15 12:54:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2804957216  

> ... we have our own gcd/lcm as a fall-back of last resort, but they're not actually very performant (I suspect anyway) compared to the full Boost versions. Or perhaps I'm wrong on that?  
  
Hmmm... That's a good one. I would be somewhat leary of incurring a performance loss for this. The real changes I implemented involve calculating the `gcd()` for integral types. These default to `::std::gcd()` (when available).  
  
I could work out some perf-tests before/after.  
  
> In any case we need to get these CI tests fixed up before we can do much of anything...   
  
Well, it _can_ go green and just did.  
  
We can also drop this one any time, it is not a critical change. But I'll invest the hour of play-time for some perf-experiments...  
  
Thanks John!

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-05-05 10:27:15 UTC  
> Updated_at: 2025-05-05 11:16:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2850564081  

>> ... we have our own gcd/lcm as a fall-back of last resort, but they're not actually very performant (I suspect anyway) compared to the full Boost versions. Or perhaps I'm wrong on that?  
  
> work out some perf-tests before/after.  
  
Well, the performance numbers took a while, but here they are. Care was taken to measure the time of the GCD operations themselves insofar as possible (maybe with extra time only for `move`-ingh them into the vector storage). So I precomputed a bunch of random `a` and `b`, stored these in a vector and then timed the `gcd(a, b)` and stored these in `c`. This is the time shown below.  
  
So it does, in fact, appear that the full Boost versions of GCD in `Boost.Integer` offer slightly superior performance. In light of this, I don't see any reason to pursue this PR any further. And I'll close it if that's the consensus?  
  
Cc: @jzmaddock and @mborland   
  
|     digits    |    runs           |    time (develop) [s]  |    time (candidate) [s] |    ratio     |  
|---------------|-------------------|------------------------|-------------------------|--------------|  
|     128       |  16 * 0x200000    |      1.65              |       1.63              |       1.00   |  
|     256       |  16 * 0x200000    |      2.20              |       2.27              |       1.03   |  
|     512       |  16 * 0x200000    |      3.48              |       3.64              |       1.05   |  
|    1024       |  16 * 0x200000    |      6.29              |       6.75              |       1.07   |  
|    4096       |  16 * 0x200000    |      23.3              |      24.6               |       1.06   |

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-05-05 10:32:41 UTC  
> Updated_at: 2025-05-05 11:35:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2850575505  

> The header has also been refactored to treat having/not-having `std::gcd` in a uniform fashion with respect to compiler switches.  
  
This part, however, might be a useful change.  
  
Cc: @jzmaddock and @mborland

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-05-05 13:09:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2850959015  

Add the source codes used for performance measurements. It is a zip archive of the source files.  
[Test.zip](https://github.com/user-attachments/files/20037410/Test.zip)

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2025-05-05 13:20:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2850990398  

Hi @jzmaddock and @mborland I've never really follwed the Circle CI build that much.  
  
For some reason, on trivial changes, the `inspect` job is failing. But I can't really see why. Do you guys know why it is failing?

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2025-05-05 14:40:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2851230294  

OK I made a bit of a little mess. I'll need a few tries to clean this up... In progress

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2025-05-05 15:19:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2851347564  

> For some reason, on trivial changes, the inspect job is failing. But I can't really see why. Do you guys know why it is failing?  
  
No, but it's started failing on Math as well.  I think something outside our control has changed, will try to take a look...

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-05-05 15:31:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2851379808  

See comment: https://github.com/boostorg/inspect/commit/0ef79484318eae34922500f0ad8b72806e33ac78#r156401450

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2025-05-05 19:56:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/676#issuecomment-2852186794  

Merging in acceptance of upstream break in `Inspect`.

---
