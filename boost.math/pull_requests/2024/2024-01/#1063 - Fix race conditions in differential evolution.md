# #1063 Fix race conditions in differential evolution [Merged]

> Username: NAThompson  
> Created at: 2024-01-09 01:20:47 UTC  
> Updated at: 2024-01-17 18:20:47 UTC  
> Merged at: 2024-01-17 18:20:41 UTC  
> Closed at: 2024-01-17 18:20:41 UTC  
> Url: https://github.com/boostorg/math/pull/1063  

Through a combination of silly mistakes, I missed a pile of race conditions in the OpenMP threading.  
  
Switch to C++ threading. Note that this change requires serial generation of trial vectors.  
  
Hopefully I can figure out to parallelize the generation of trial vectors to reduce the serial section a la Ahmdahl's law,  
while simultaneously keeping thread sanitizer happy.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2024-01-09 18:52:08 UTC  
> Updated_at: 2024-01-09 18:55:22 UTC  
> Url: https://github.com/boostorg/math/pull/1063#issuecomment-1883604195  

@jzmaddock : I wonder if we could revisit the "no unicode in comments" rule-I had to remove all the accents from the author's names which I found somewhat inelegant. Going a bit further, maybe something like:  
  
```cpp  
template<typename ℝ>  
```  
  
could really improve readability. I know we can use `// boost-no-inspect`, but the other inspection rules are quite valuable.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-01-10 06:54:09 UTC  
> Url: https://github.com/boostorg/math/pull/1063#issuecomment-1884287737  

> @jzmaddock : I wonder if we could revisit the "no unicode in comments" rule-I had to remove all the accents from the author's names which I found somewhat inelegant. Going a bit further, maybe something like:  
>   
> ```c++  
> template<typename ℝ>  
> ```  
>   
> could really improve readability. I know we can use `// boost-no-inspect`, but the other inspection rules are quite valuable.  
  
My two cents is I think revisiting the no unicode in comments would be good, but adding unicode into the actual code is not. SO says it varies from formerly illegal to just unsupported now: https://stackoverflow.com/questions/26660180/are-unicode-and-special-characters-in-variable-names-in-clang-not-allowed.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-01-10 09:17:31 UTC  
> Url: https://github.com/boostorg/math/pull/1063#issuecomment-1884464053  

I don't know if it's still the case (but I assume it is) that users on Windows in far eastern countries do not use Unicode character sets, and Unicode in source files (even in comments) renders them non-compilable.  If you search hard enough you'll find some old bug reports on this which crop up whenever Unicode has crept in accidentally.  Usually been my fault in the past, and usually for authors names as in your case here.  Whatever we should run this past the mailing list rather than making a unilateral change?

---

## Comment 4

> Username: NAThompson  
> Created_at: 2024-01-10 18:17:10 UTC  
> Url: https://github.com/boostorg/math/pull/1063#issuecomment-1885378959  

@jzmaddock , @mborland : I do enjoy the code readability improvements that unicode can bring, but it seems like this is an ax I probably shouldn't grind . . .

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2024-01-17 00:02:19 UTC  
> Updated_at: 2024-01-17 18:12:18 UTC  
> Url: https://github.com/boostorg/math/pull/1063#issuecomment-1894710384  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `115 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`79b4015`)](https://app.codecov.io/gh/boostorg/math/commit/79b4015d4d048a784ba341f40d6fa58c43087ec7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.31% compared to head [(`da1fdc7`)](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.22%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1063/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1063      +/-   ##  
===========================================  
- Coverage    85.31%   85.22%   -0.10%       
===========================================  
  Files          873      875       +2       
  Lines        66532    66568      +36       
===========================================  
- Hits         56765    56733      -32       
- Misses        9767     9835      +68       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_functions\_for\_optimization.hpp](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Z1bmN0aW9uc19mb3Jfb3B0aW1pemF0aW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/math\_unit\_test.hpp](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9tYXRoX3VuaXRfdGVzdC5ocHA=) | `24.00% <33.33%> (+0.50%)` | :arrow_up: |  
| [...boost/math/optimization/differential\_evolution.hpp](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9kaWZmZXJlbnRpYWxfZXZvbHV0aW9uLmhwcA==) | `82.00% <82.00%> (ø)` | |  
| [test/differential\_evolution\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9kaWZmZXJlbnRpYWxfZXZvbHV0aW9uX3Rlc3QuY3Bw) | `59.70% <55.93%> (-40.30%)` | :arrow_down: |  
| [include/boost/math/optimization/detail/common.hpp](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9kZXRhaWwvY29tbW9uLmhwcA==) | `26.96% <26.96%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1063/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [79b4015...da1fdc7](https://app.codecov.io/gh/boostorg/math/pull/1063?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 6 [Commented]

> Username: mborland  
> Created_at: 2024-01-17 07:44:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1063#pullrequestreview-1826668984  

TSAN and Helgrind both seem happy now. My one concern is that codecov shows that all the regions that involve synchronization are not covered by tests. Is this due to GHA not having enough threads available? It would probably be good to cover all of your param checks, and testing of validating your initial guess.

---
