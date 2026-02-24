# #838 Antora docs: modify dir copy step [Merged]

> Username: sdarwin  
> Created at: 2024-04-08 19:23:52 UTC  
> Updated at: 2024-04-09 01:42:36 UTC  
> Merged at: 2024-04-09 00:51:28 UTC  
> Closed at: 2024-04-09 00:51:28 UTC  
> Url: https://github.com/boostorg/url/pull/838  

This is to solve a case where selfDir is not literally "url" but might be "url-docs" or similar. Which is why the `${selfDir}` variable was added, to start with.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-04-08 19:33:49 UTC  
> Url: https://github.com/boostorg/url/pull/838#issuecomment-2043508811  

An automated preview of the documentation is available at [https://838.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://838.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-04-08 19:41:08 UTC  
> Url: https://github.com/boostorg/url/pull/838#issuecomment-2043518210  

Antora version: an automated preview of the documentation is available at [https://838.urlantora.prtest2.cppalliance.org/site/index.html](https://838.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-04-08 19:46:31 UTC  
> Updated_at: 2024-04-08 19:50:56 UTC  
> Url: https://github.com/boostorg/url/pull/838#issuecomment-2043525257  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/838?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`fddf6b7`)](https://app.codecov.io/gh/boostorg/url/commit/fddf6b75d5fffe45c016a0787cc951bda779772b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b4b10fb`)](https://app.codecov.io/gh/boostorg/url/pull/838?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/838/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/838?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #838   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          157      157             
  Lines         8422     8422             
========================================  
  Hits          8356     8356             
  Misses          66       66             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/838?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/838?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fddf6b7...b4b10fb](https://app.codecov.io/gh/boostorg/url/pull/838?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-04-08 19:49:06 UTC  
> Url: https://github.com/boostorg/url/pull/838#issuecomment-2043528592  

GCOVR code coverage report [https://838.url.prtest2.cppalliance.org/gcovr/index.html](https://838.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://838.url.prtest2.cppalliance.org/genhtml/index.html](https://838.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://838.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://838.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2024-04-09 00:52:02 UTC  
> Url: https://github.com/boostorg/url/pull/838#issuecomment-2043945959  

Is the machine used to build the [preview](https://838.urlantora.prtest2.cppalliance.org/site/index.html) using Ubuntu 22.04?

---

## Comment 6

> Username: sdarwin  
> Created_at: 2024-04-09 01:42:35 UTC  
> Url: https://github.com/boostorg/url/pull/838#issuecomment-2043992566  

> [preview](https://838.urlantora.prtest2.cppalliance.org/site/index.html) using Ubuntu 22.04?  
  
Yes

---
