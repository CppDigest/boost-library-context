# #869 build: exceptions are not an explicit default [Merged]

> Username: alandefreitas  
> Created at: 2024-10-14 15:07:42 UTC  
> Updated at: 2024-10-14 18:27:07 UTC  
> Merged at: 2024-10-14 18:26:30 UTC  
> Closed at: 2024-10-14 18:26:30 UTC  
> Url: https://github.com/boostorg/url/pull/869  

fix #868

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-10-14 15:14:50 UTC  
> Url: https://github.com/boostorg/url/pull/869#issuecomment-2411563190  

Antora version: an automated preview of the documentation is available at [https://869.urlantora.prtest2.cppalliance.org/site/index.html](https://869.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-10-14 15:32:23 UTC  
> Updated_at: 2024-10-14 18:27:07 UTC  
> Url: https://github.com/boostorg/url/pull/869#issuecomment-2411607238  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/869?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.19%. Comparing base [(`b6688d9`)](https://app.codecov.io/gh/boostorg/url/commit/b6688d9fbb47fa917a294022c443af40e0c8d7ca?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dc6c40d`)](https://app.codecov.io/gh/boostorg/url/commit/dc6c40d438f4b4146a523c4bc3158f7a65157cea?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/869/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/869?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #869   +/-   ##  
========================================  
  Coverage    99.19%   99.19%             
========================================  
  Files          157      157             
  Lines         8415     8415             
========================================  
  Hits          8347     8347             
  Misses          68       68             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/869?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/869?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b6688d9...dc6c40d](https://app.codecov.io/gh/boostorg/url/pull/869?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-10-14 15:32:38 UTC  
> Url: https://github.com/boostorg/url/pull/869#issuecomment-2411607752  

GCOVR code coverage report [https://869.url.prtest2.cppalliance.org/gcovr/index.html](https://869.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://869.url.prtest2.cppalliance.org/genhtml/index.html](https://869.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://869.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://869.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2024-10-14 16:21:55 UTC  
> Url: https://github.com/boostorg/url/pull/869#issuecomment-2411714606  

misleading commit messages. suggested:  
  
"exceptions are not an explicit default"

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-10-14 16:32:01 UTC  
> Url: https://github.com/boostorg/url/pull/869#issuecomment-2411739152  

Antora version: an automated preview of the documentation is available at [https://869.urlantora.prtest2.cppalliance.org/site/index.html](https://869.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-10-14 16:47:08 UTC  
> Url: https://github.com/boostorg/url/pull/869#issuecomment-2411769363  

GCOVR code coverage report [https://869.url.prtest2.cppalliance.org/gcovr/index.html](https://869.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://869.url.prtest2.cppalliance.org/genhtml/index.html](https://869.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://869.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://869.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
