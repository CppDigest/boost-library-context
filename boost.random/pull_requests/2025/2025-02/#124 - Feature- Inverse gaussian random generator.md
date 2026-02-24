# #124 Feature: Inverse gaussian random generator [Merged]

> Username: ygeunkim  
> Created at: 2025-02-01 03:59:06 UTC  
> Updated at: 2025-02-04 01:18:03 UTC  
> Merged at: 2025-02-04 01:17:53 UTC  
> Closed at: 2025-02-04 01:17:53 UTC  
> Url: https://github.com/boostorg/random/pull/124  

Since [math](https://www.boost.org/doc/libs/1_73_0/libs/math/doc/html/math_toolkit/dist_ref/dists/inverse_gaussian_dist.html) module has [Inverse Gaussian distribution](https://en.wikipedia.org/wiki/Inverse_Gaussian_distribution#Sampling_from_an_inverse-Gaussian_distribution), it might be worth adding its random generator.  
  
- Added `random/inverse_gaussian_distribution.hpp` header  
- Added `test/test_inverse_gaussian_distribution.cpp`

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-01 04:08:44 UTC  
> Updated_at: 2025-02-04 01:18:03 UTC  
> Url: https://github.com/boostorg/random/pull/124#issuecomment-2628755522  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/124?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.77%. Comparing base [(`4a4cdfe`)](https://app.codecov.io/gh/boostorg/random/commit/4a4cdfe410a4afd0b97b8a7956e31825a2cd2978?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1b7af50`)](https://app.codecov.io/gh/boostorg/random/commit/1b7af504523636906859fb05aa0fb4a19ac33564?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 19 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/124/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/124?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #124      +/-   ##  
===========================================  
+ Coverage    95.74%   95.77%   +0.03%       
===========================================  
  Files          112      113       +1       
  Lines         7941     8000      +59       
===========================================  
+ Hits          7603     7662      +59       
  Misses         338      338                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/124?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/random/inverse\_gaussian\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/124?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Finverse_gaussian_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vaW52ZXJzZV9nYXVzc2lhbl9kaXN0cmlidXRpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/124?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/124?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4a4cdfe...1b7af50](https://app.codecov.io/gh/boostorg/random/pull/124?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2025-02-03 15:29:09 UTC  
> Updated_at: 2025-02-03 18:00:18 UTC  
> Url: https://github.com/boostorg/random/pull/124#issuecomment-2631331937  

@ygeunkim Take a look at the commits that I added. The Jamfile is what actually specifies the tests to run. I ran into a couple errors of duplicate operator>> and operator<< and missing test macros so I added those. I also added the class to the docs and linked the file to oxygen. If you want to change the description I added feel free.

---

## Comment 3

> Username: ygeunkim  
> Created_at: 2025-02-04 01:02:53 UTC  
> Url: https://github.com/boostorg/random/pull/124#issuecomment-2632524954  

@mborland Thanks! Everything seems fine🙂 I'll fix #126 looking at your commits.

---

## Comment 4

> Username: mborland  
> Created_at: 2025-02-04 01:17:32 UTC  
> Url: https://github.com/boostorg/random/pull/124#issuecomment-2632542732  

> @mborland Thanks! Everything seems fine🙂 I'll fix #126 looking at your commits.  
  
Perfect. I'll merge this so you can rebase that one on develop. I expect you'll get merge conflicts by adding tests to the Jamfile and docs.

---
