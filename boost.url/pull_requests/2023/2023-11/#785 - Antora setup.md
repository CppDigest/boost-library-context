# #785 Antora setup [Merged]

> Username: alandefreitas  
> Created at: 2023-11-06 21:49:35 UTC  
> Updated at: 2023-11-07 13:40:47 UTC  
> Merged at: 2023-11-07 13:40:47 UTC  
> Closed at: 2023-11-07 13:40:47 UTC  
> Url: https://github.com/boostorg/url/pull/785  

This PR sets up Antora support for Boost.URL. The main documentation content is moved from quickbook to asciidoc. Some other content, such as snippets and the reference still need custom extensions.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2023-11-06 21:51:20 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796682270  

@sdarwin Is it possible to set up another preview that runs doc/build_antora.sh instead while this Antora transition is not complete?

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-11-06 21:58:06 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796763260  

An automated preview of the documentation is available at [https://785.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://785.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-11-06 22:11:22 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796842194  

GCOVR code coverage report [https://785.url.prtest2.cppalliance.org/gcovr/index.html](https://785.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://785.url.prtest2.cppalliance.org/genhtml/index.html](https://785.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://785.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://785.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2023-11-06 22:12:00 UTC  
> Updated_at: 2023-11-06 22:56:47 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796843990  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/785?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#785](https://app.codecov.io/gh/boostorg/url/pull/785?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8c10bd9) into [develop](https://app.codecov.io/gh/boostorg/url/commit/7e47e9fef6fecce45f7c65277601b7e7ff38c365?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7e47e9f) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/785/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/785?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #785   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/785?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/785?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7e47e9f...8c10bd9](https://app.codecov.io/gh/boostorg/url/pull/785?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-11-06 22:32:49 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796952219  

An automated preview of the documentation is available at [https://785.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://785.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-11-06 22:42:57 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796972671  

An automated preview of the documentation is available at [https://785.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://785.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-11-06 22:45:35 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796976570  

GCOVR code coverage report [https://785.url.prtest2.cppalliance.org/gcovr/index.html](https://785.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://785.url.prtest2.cppalliance.org/genhtml/index.html](https://785.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://785.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://785.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-11-06 23:01:42 UTC  
> Url: https://github.com/boostorg/url/pull/785#issuecomment-1796999703  

GCOVR code coverage report [https://785.url.prtest2.cppalliance.org/gcovr/index.html](https://785.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://785.url.prtest2.cppalliance.org/genhtml/index.html](https://785.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://785.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://785.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
