# #120 Replace `BOOST_CHARCONV_STD_ERANGE` with `from_chars_strict` [Merged]

> Username: mborland  
> Created at: 2024-01-17 09:51:01 UTC  
> Updated at: 2024-01-18 06:58:17 UTC  
> Merged at: 2024-01-18 06:58:14 UTC  
> Closed at: 2024-01-18 06:58:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/120  

Closes: #110

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-01-17 10:01:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/120#issuecomment-1895473316  

An automated preview of the documentation is available at [https://120.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://120.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-01-17 10:16:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/120#issuecomment-1895500608  

An automated preview of the documentation is available at [https://120.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://120.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-01-17 10:29:50 UTC  
> Updated_at: 2024-01-17 11:45:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/120#issuecomment-1895522886  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
Attention: `1 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`06d8347`)](https://app.codecov.io/gh/cppalliance/charconv/commit/06d834728ae8970473f6ef18c079f0e7fae7b071?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.02% compared to head [(`d8a0d2b`)](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.25%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/120/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #120      +/-   ##  
===========================================  
+ Coverage    88.02%   88.25%   +0.22%       
===========================================  
  Files           55       55                
  Lines         7861     7893      +32       
===========================================  
+ Hits          6920     6966      +46       
+ Misses         941      927      -14       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/from\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9mcm9tX2NoYXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.58% <ø> (ø)` | |  
| [test/github\_issue\_110.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTEwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_boost\_json\_values.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Jvb3N0X2pzb25fdmFsdWVzLmNwcA==) | `99.59% <ø> (ø)` | |  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `96.42% <90.90%> (-1.35%)` | :arrow_down: |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/120/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [06d8347...d8a0d2b](https://app.codecov.io/gh/cppalliance/charconv/pull/120?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-01-17 11:06:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/120#issuecomment-1895584906  

An automated preview of the documentation is available at [https://120.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://120.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
