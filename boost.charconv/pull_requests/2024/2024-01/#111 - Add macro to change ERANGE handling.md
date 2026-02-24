# #111 Add macro to change ERANGE handling.  [Merged]

> Username: mborland  
> Created at: 2024-01-04 12:58:32 UTC  
> Updated at: 2024-01-05 07:52:39 UTC  
> Merged at: 2024-01-05 07:52:35 UTC  
> Closed at: 2024-01-05 07:52:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/111  

Defaults to the current behavior for handling ERANGE, but allows a user to match the STL if they so choose.  
  
Closes: #110

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-01-04 13:06:04 UTC  
> Url: https://github.com/boostorg/charconv/pull/111#issuecomment-1877066740  

An automated preview of the documentation is available at [https://111.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://111.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-01-05 06:21:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/111#issuecomment-1878183820  

An automated preview of the documentation is available at [https://111.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://111.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-01-05 06:41:04 UTC  
> Url: https://github.com/boostorg/charconv/pull/111#issuecomment-1878200693  

An automated preview of the documentation is available at [https://111.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://111.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-01-05 07:07:05 UTC  
> Updated_at: 2024-01-05 07:21:40 UTC  
> Url: https://github.com/boostorg/charconv/pull/111#issuecomment-1878226120  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`1927ac9`)](https://app.codecov.io/gh/cppalliance/charconv/commit/1927ac946d08ab999d9f40e613046a80552c4a94?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 86.22% compared to head [(`070c83b`)](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 86.22%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/111/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #111   +/-   ##  
========================================  
  Coverage    86.22%   86.22%             
========================================  
  Files           54       55    +1       
  Lines         7889     7891    +2       
========================================  
+ Hits          6802     6804    +2       
  Misses        1087     1087             
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `97.14% <ø> (ø)` | |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.51% <ø> (ø)` | |  
| [test/github\_issue\_110.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTEwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_boost\_json\_values.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Jvb3N0X2pzb25fdmFsdWVzLmNwcA==) | `97.20% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [1927ac9...070c83b](https://app.codecov.io/gh/cppalliance/charconv/pull/111?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
