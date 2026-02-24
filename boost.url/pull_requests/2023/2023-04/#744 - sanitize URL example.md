# #744 sanitize URL example [Merged]

> Username: alandefreitas  
> Created at: 2023-04-26 23:44:13 UTC  
> Updated at: 2023-07-24 14:37:49 UTC  
> Merged at: 2023-04-27 20:07:09 UTC  
> Closed at: 2023-04-27 20:07:09 UTC  
> Url: https://github.com/boostorg/url/pull/744  

This PR fixes #742 and helps us provide support for related recurring issues.  
  
This example parses a non-strict/invalid URL into path components according to its delimiters and sanitizes it. This pattern can be adapted to the requirements of other applications.  
  
Once the non-strict components are determined, a new URL is created and its parts are set with the `set_encoded_X` functions, which will encode any invalid chars accordingly.  
  
This sort of transformation is useful in applications that are extremely loose in what kinds of URLs they accept, such as browsers. The sanitized URL can later be used for machine-to-machine communication.  
  
Using non-strict URLs directly is a security concern in machine-to-machine communication, is ambiguous, and also involve an extra cost for the transformations.  
  
Different transformations are required by different applications to construct a valid URL appropriate for machine-to-machine communication in that context. For instance, if an invalid relative reference includes something that looks like a host in the first path segment, browsers usually interpret that as the host with an implicit "https" scheme. Other applications also have other implicit schemes.  
  
The example also identifies whether the input is already valid. It includes diagnostics that can be used to help the user determine if a URL is invalid and why it's invalid.  
  
Once all transformations are applied, the result is a URL appropriate for machine-to-machine communication.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-26 23:52:11 UTC  
> Url: https://github.com/boostorg/url/pull/744#issuecomment-1524224772  

An automated preview of the documentation is available at [https://744.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://744.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-04-27 00:02:28 UTC  
> Url: https://github.com/boostorg/url/pull/744#issuecomment-1524242068  

GCOVR code coverage report [https://744.url.prtest.cppalliance.org/gcovr/index.html](https://744.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://744.url.prtest.cppalliance.org/genhtml/index.html](https://744.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://744.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://744.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-04-27 00:06:50 UTC  
> Updated_at: 2023-04-27 00:11:22 UTC  
> Url: https://github.com/boostorg/url/pull/744#issuecomment-1524249867  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/744?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#744](https://codecov.io/gh/boostorg/url/pull/744?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2e6cc8a) into [develop](https://codecov.io/gh/boostorg/url/commit/c518dbaa04a6557314c8c47df8eba6b9db451644?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c518dba) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/744/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/744?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #744   +/-   ##  
========================================  
  Coverage    97.25%   97.25%             
========================================  
  Files          155      155             
  Lines         8517     8517             
========================================  
  Hits          8283     8283             
  Misses         234      234             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/744?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/744?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c518dba...2e6cc8a](https://codecov.io/gh/boostorg/url/pull/744?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
