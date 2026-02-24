# #852 docs: tagfiles extension is npm package [Merged]

> Username: alandefreitas  
> Created at: 2024-07-25 23:35:58 UTC  
> Updated at: 2024-07-26 20:59:40 UTC  
> Merged at: 2024-07-26 20:59:40 UTC  
> Closed at: 2024-07-26 20:59:40 UTC  
> Url: https://github.com/boostorg/url/pull/852  

This PR moves the c++ tagfiles extension out of the Boost.URL so it can also be included by the playbook project in website-v2-docs and all components that might need it. The npm package also simplifies the dependencies and allows the extension to have its own documentation and unit tests.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-25 23:42:06 UTC  
> Url: https://github.com/boostorg/url/pull/852#issuecomment-2251577729  

Antora version: an automated preview of the documentation is available at [https://852.urlantora.prtest2.cppalliance.org/site/index.html](https://852.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-07-25 23:50:24 UTC  
> Url: https://github.com/boostorg/url/pull/852#issuecomment-2251585501  

An automated preview of the documentation is available at [https://852.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://852.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-07-25 23:52:19 UTC  
> Updated_at: 2024-07-25 23:57:49 UTC  
> Url: https://github.com/boostorg/url/pull/852#issuecomment-2251587898  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/852?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`ab3cce2`)](https://app.codecov.io/gh/boostorg/url/commit/ab3cce2709500b0d142b076f0f46fad7b9ccc16f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`671cdd6`)](https://app.codecov.io/gh/boostorg/url/commit/671cdd6a3d856413411ea49333260beb866f93c8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/852/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/852?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #852   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/852?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/852?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ab3cce2...671cdd6](https://app.codecov.io/gh/boostorg/url/pull/852?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-07-25 23:56:41 UTC  
> Url: https://github.com/boostorg/url/pull/852#issuecomment-2251592328  

GCOVR code coverage report [https://852.url.prtest2.cppalliance.org/gcovr/index.html](https://852.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://852.url.prtest2.cppalliance.org/genhtml/index.html](https://852.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://852.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://852.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
