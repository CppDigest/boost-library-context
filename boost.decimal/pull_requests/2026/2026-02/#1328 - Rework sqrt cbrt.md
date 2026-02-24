# #1328 Rework sqrt cbrt [Merged]

> Username: ckormanyos  
> Created at: 2026-02-02 09:35:12 UTC  
> Updated at: 2026-02-03 09:51:52 UTC  
> Merged at: 2026-02-03 09:51:04 UTC  
> Closed at: 2026-02-03 09:51:04 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328  

This PR reworks and improves the existing `sqrt` and `cbrt` impls.  
  
- For `sqrt` reduce the argument further to $1/16 < x {\leq} 1/4$ and run the Pade approximation at $1/8$.  
- For `cbrt` reduce the argument further to $1/64 < x {\leq} 1/8$ and run the Pade approximation at $1/16$.  
- Reduce the loops of Newton iterations on both to $2,3,4$, down from $3,4,5$.  
- Decrease the $128$-bit tolerance of `cbrt` to expected value.  
- Do some sync-up of the imps `sqrt` and `cbrt`.  
  
This is one that I've had planned for a while. I think it's about as good as I'm going to get it with the current technology.  
  
Note that these calculations are still slow and could _DEFINITELY_ use efficiency improvements, for instance with a better initial guess.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2026-02-02 09:36:51 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#issuecomment-3834006650  

Oh hi @mborland I don't know if we are supposed to work in the Alliance repo or Boost. I thought this was in the Alliance, but I'm mistakenly in the boost repo

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2026-02-02 09:44:26 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#issuecomment-3834040799  

See also #1311

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2026-02-02 09:53:46 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#issuecomment-3834084926  

Looking at the Boost calendar, we have until the 18th for new libs and another week for larger changes. If this one fits, it might be nice. Let's see if it seems stable.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2026-02-02 11:34:14 UTC  
> Updated_at: 2026-02-02 11:35:46 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#issuecomment-3834590825  

Hi Matt (@mborland) I also added some trial counts to the root tests. So if this unduly bogs down some of the slow runners, then we can walk those changes down.  
  
Do please keep an eye out for anything like excessively increased CI/CD time. I see recent total times arond 3 hours. So we need to see if I added to much to the sanitizers or something weird like that.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2026-02-02 12:13:30 UTC  
> Updated_at: 2026-02-03 09:51:52 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#issuecomment-3834766667  

## [Codecov](https://app.codecov.io/gh/boostorg/decimal/pull/1328?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.8%. Comparing base ([`be078b6`](https://app.codecov.io/gh/boostorg/decimal/commit/be078b6a2b5ee10d4791ab400031e827077c0f2b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`4f156cd`](https://app.codecov.io/gh/boostorg/decimal/commit/4f156cdd71459ebd44b3feeb17e892c14a5fb8c9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/decimal/pull/1328/graphs/tree.svg?width=650&height=150&src=pr&token=SkjASiv9Df&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/decimal/pull/1328?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1328     +/-   ##  
=========================================  
+ Coverage     98.8%   98.8%   +0.1%       
=========================================  
  Files          278     278               
  Lines        18013   18034     +21       
  Branches      1916    1918      +2       
=========================================  
+ Hits         17790   17813     +23       
+ Misses         223     221      -2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/decimal/pull/1328?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/detail/cmath/cbrt.hpp](https://app.codecov.io/gh/boostorg/decimal/pull/1328?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fcbrt.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9jYnJ0LmhwcA==) | `100.0% <100.0%> (+1.7%)` | :arrow_up: |  
| [include/boost/decimal/detail/cmath/sqrt.hpp](https://app.codecov.io/gh/boostorg/decimal/pull/1328?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fsqrt.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9zcXJ0LmhwcA==) | `100.0% <100.0%> (+2.5%)` | :arrow_up: |  
| [test/test\_cbrt.cpp](https://app.codecov.io/gh/boostorg/decimal/pull/1328?src=pr&el=tree&filepath=test%2Ftest_cbrt.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NicnQuY3Bw) | `100.0% <100.0%> (ø)` | |  
| [test/test\_sqrt.cpp](https://app.codecov.io/gh/boostorg/decimal/pull/1328?src=pr&el=tree&filepath=test%2Ftest_sqrt.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3NxcnQuY3Bw) | `100.0% <100.0%> (ø)` | |  
  
... and [7 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/decimal/pull/1328/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/decimal/pull/1328?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/decimal/pull/1328?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [be078b6...4f156cd](https://app.codecov.io/gh/boostorg/decimal/pull/1328?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 6

> Username: mborland  
> Created_at: 2026-02-02 14:40:53 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#issuecomment-3835536375  

> Oh hi @mborland I don't know if we are supposed to work in the Alliance repo or Boost. I thought this was in the Alliance, but I'm mistakenly in the boost repo  
  
The alliance repo was moved into boost now that we've been accepted and release is coming up. Your access should be exactly the same.

---

## Review 7 [Commented]

> Username: mborland  
> Created_at: 2026-02-02 14:46:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/decimal/pull/1328#pullrequestreview-3739856624  

📁 test/test_cbrt.cpp

```diff
   6 |- #include "testing_config.hpp"
   7 |- #include <boost/decimal.hpp>
   8 |- 
```

> Username: mborland  
> Created_at: 2026-02-02 14:42:08 UTC  
> Updated_at: 2026-02-02 14:46:05 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754686153  

I like having these above the warning suppressions, because then our tests catch warnings from our libs, but not others in boost. I think Core was throwing a warning we track.

> Username: ckormanyos  
> Created_at: 2026-02-02 15:27:46 UTC  
> Updated_at: 2026-02-02 15:27:47 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754917335  

OK fine. Will change it


📁 test/test_sqrt.cpp

```diff
  12 | #endif
  13 | 
  14 |+ #include "testing_config.hpp"
```

> Username: mborland  
> Created_at: 2026-02-02 14:42:48 UTC  
> Updated_at: 2026-02-02 14:46:05 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754689690  

Same as above. The STL shouldn't throw warnings

> Username: ckormanyos  
> Created_at: 2026-02-02 15:32:41 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754939489  

OK I will revert.


📁 include/boost/decimal/detail/cmath/cbrt.hpp

```diff
 193 | BOOST_DECIMAL_EXPORT template <typename T>
 184 |- constexpr auto cbrt(const T val) noexcept
 194 |+ constexpr auto cbrt(const T val) noexcept // LCOV_EXCL_LINE
```

> Username: mborland  
> Created_at: 2026-02-02 14:45:59 UTC  
> Updated_at: 2026-02-02 14:46:05 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754705561  

One thing I have been thinking on and off about is should we take Intel's approach to implementation when we can? For example they do this:  
  
```c++  
auto cbrt(decimal32_t val) noexcept  
{  
    return static_cast<decimal32_t>(std::cbrt(static_cast<double>(val)));  
}  
```  
  
`double` exceeds the precision of `decimal32_t`, and an x64 80-bit long double exceeds the precision of `decimal64_t`. Intel assumes the existence of `_Quad` (since it always ships with their compiler) and uses that for `decimal128_t`.   
  
In order to make everything still `constexpr` we would need to guard these by consteval detection. Thoughts?

> Username: ckormanyos  
> Created_at: 2026-02-02 15:30:38 UTC  
> Updated_at: 2026-02-02 15:31:16 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754930439  

Indeed. We stick ruthlessly to classical algorithms, when hardware roots are right off the tree.  
  
I would clean up this PR. It's about as good as Pade + Newton will get.  
  
For 1.92, we can decide if we would like to use std-lib funcs for any impls. 75% of me just does not want to change anything. We have `constexpr` and nobody is really complaining about the speed of our roots or logarithms (except me). Doing nothing other than keeping going is ultimately how I would vote in a pinch.

> Username: mborland  
> Created_at: 2026-02-02 15:32:35 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754939056  

That's fine with me. It would take a bunch of language workarounds to get right, so defer until people need it is the correct move.

> Username: ckormanyos  
> Created_at: 2026-02-02 15:41:18 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2754978446  

You read my mind as usual

> Username: ckormanyos  
> Created_at: 2026-02-02 17:02:09 UTC  
> Url: https://github.com/boostorg/decimal/pull/1328#discussion_r2755357659  

Let's keep it as an idea though, but definitely in the category of long-term library evolution.


---
