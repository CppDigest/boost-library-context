# #201 cmake subdir tests in drone [Merged]

> Username: sdarwin  
> Created at: 2022-06-14 20:22:16 UTC  
> Updated at: 2022-06-15 21:27:12 UTC  
> Merged at: 2022-06-15 17:59:28 UTC  
> Closed at: 2022-06-15 17:59:28 UTC  
> Url: https://github.com/boostorg/url/pull/201  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-15 01:48:43 UTC  
> Url: https://github.com/boostorg/url/pull/201#issuecomment-1155885076  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/201?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#201](https://codecov.io/gh/CPPAlliance/url/pull/201?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (07f0883) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/9f4d6ef7ca1093069206b0f128373d69bc2800cb?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (9f4d6ef) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/201/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/201?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #201   +/-   ##  
========================================  
  Coverage    96.73%   96.73%             
========================================  
  Files          119      119             
  Lines         6056     6056             
========================================  
  Hits          5858     5858             
  Misses         198      198             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/201?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/201?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [9f4d6ef...07f0883](https://codecov.io/gh/CPPAlliance/url/pull/201?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2022-06-15 02:06:32 UTC  
> Updated_at: 2022-06-15 02:06:40 UTC  
> Url: https://github.com/boostorg/url/pull/201#issuecomment-1155894869  

Nice. This means we have the "[Run CMake install tests](https://github.com/boostorg/boost-ci/blob/a5712f1268770e1a32e352d485247d0460145a3f/.github/workflows/ci.yml#L411)" but not the "[Run CMake subdir tests](https://github.com/boostorg/boost-ci/blob/a5712f1268770e1a32e352d485247d0460145a3f/.github/workflows/ci.yml#L395)", right?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2022-06-15 14:44:47 UTC  
> Updated_at: 2022-06-15 14:45:05 UTC  
> Url: https://github.com/boostorg/url/pull/201#issuecomment-1156566633  

@alandefreitas the only difference between the tests you mentioned is the presence of "-DBOOST_CI_INSTALL_TEST=ON", which is boost-ci specific.   Since the new test here doesn't have "install" or BOOST_CI_INSTALL_TEST, it corresponds more to "cmake subdir tests" rather than "cmake install tests".  
  
The way to run a "cmake install test" is to specify "--target install".   That is configured on another test already.

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-06-15 17:59:04 UTC  
> Url: https://github.com/boostorg/url/pull/201#issuecomment-1156770662  

Thanks! I'll replicate the other test then :)

---

## Comment 5

> Username: sdarwin  
> Created_at: 2022-06-15 19:58:39 UTC  
> Url: https://github.com/boostorg/url/pull/201#issuecomment-1156874666  

@alandefreitas a good example of cmake tests could be pdimov's github actions such as https://github.com/boostorg/core/blame/develop/.github/workflows/ci.yml

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-06-15 20:06:26 UTC  
> Url: https://github.com/boostorg/url/pull/201#issuecomment-1156880901  

Nice. Thanks for this reference. I've been already experimenting and trying to replicate the github step in another branch.  
  
By the way, way does drone run the alandefreitas/socks scripts when I push to the repo but only runs the alandefreitas/url scripts when I open an internal PR? Is this some configuration in github I'm missing?

---

## Comment 7

> Username: sdarwin  
> Created_at: 2022-06-15 21:27:12 UTC  
> Url: https://github.com/boostorg/url/pull/201#issuecomment-1156962708  

Drone is building on pushes to these branches.   
```  
{ "branch": [ "master", "develop", "drone*", "bugfix/*", "feature/*", "fix/*", "pr/*" ] }  
```  
Set the branch to "feature/_something_" to trigger a build.    
In the past it has been building different branches: https://drone.cpp.al/alandefreitas/url

---
