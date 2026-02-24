# #911 build: cmake preferred version is 3.20 [Merged]

> Username: alandefreitas  
> Created at: 2025-05-21 19:01:16 UTC  
> Updated at: 2025-05-21 20:51:23 UTC  
> Merged at: 2025-05-21 20:51:05 UTC  
> Closed at: 2025-05-21 20:51:05 UTC  
> Url: https://github.com/boostorg/url/pull/911  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-05-21 19:06:26 UTC  
> Url: https://github.com/boostorg/url/pull/911#issuecomment-2898952467  

An automated preview of the documentation is available at [https://911.url.prtest2.cppalliance.org/index.html](https://911.url.prtest2.cppalliance.org/index.html)

---

## Comment 2

> Username: ashtum  
> Created_at: 2025-05-21 19:07:27 UTC  
> Url: https://github.com/boostorg/url/pull/911#issuecomment-2898954717  

@alandefreitas, If you want to get rid of that CMake version warning, something like  
```  
cmake_minimum_required(VERSION 3.8...3.20)    
```  
would work as well.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-05-21 19:11:29 UTC  
> Url: https://github.com/boostorg/url/pull/911#issuecomment-2898963581  

Changing `cmake_minimum_required(VERSION 3.5...3.16)` to `cmake_minimum_required(VERSION 3.10)` is wrong. The former means "give an error on < 3.5, otherwise use 3.16 behavior." The latter means "give an error on < 3.10, otherwise use 3.10 behavior." I doubt that you really want that.  
  
`cmake_minimum_required(VERSION 3.8...3.20)` as suggested above is much better. Replace 3.20 with whatever version you are testing with.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-05-21 19:19:57 UTC  
> Url: https://github.com/boostorg/url/pull/911#issuecomment-2898982679  

GCOVR code coverage report [https://911.url.prtest2.cppalliance.org/gcovr/index.html](https://911.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://911.url.prtest2.cppalliance.org/genhtml/index.html](https://911.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://911.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://911.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-05-21 19:26:19 UTC  
> Url: https://github.com/boostorg/url/pull/911#issuecomment-2899006061  

An automated preview of the documentation is available at [https://911.url.prtest2.cppalliance.org/index.html](https://911.url.prtest2.cppalliance.org/index.html)

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2025-05-21 19:44:53 UTC  
> Updated_at: 2025-05-21 20:51:22 UTC  
> Url: https://github.com/boostorg/url/pull/911#issuecomment-2899057113  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/911?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`2e72afd`)](https://app.codecov.io/gh/boostorg/url/commit/2e72afd0e6ceb178c6aef26b598b40258b42b430?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4f894e2`)](https://app.codecov.io/gh/boostorg/url/commit/4f894e243d93b5400966c677e33a84e2ed1148d3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/911/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/911?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #911   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          158      158             
  Lines         8495     8495             
========================================  
  Hits          8428     8428             
  Misses          67       67             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/911?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/911?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2e72afd...4f894e2](https://app.codecov.io/gh/boostorg/url/pull/911?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
