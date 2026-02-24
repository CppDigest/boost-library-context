# #167 Fix to_chars errc value for overflow [Merged]

> Username: mborland  
> Created at: 2024-02-20 07:34:54 UTC  
> Updated at: 2024-02-20 08:58:57 UTC  
> Merged at: 2024-02-20 08:58:50 UTC  
> Closed at: 2024-02-20 08:58:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/167  

Partially addresses: #166

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-02-20 07:45:55 UTC  
> Url: https://github.com/boostorg/charconv/pull/167#issuecomment-1953643426  

An automated preview of the documentation is available at [https://167.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://167.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-02-20 08:00:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/167#issuecomment-1953662210  

An automated preview of the documentation is available at [https://167.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://167.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-02-20 08:29:15 UTC  
> Updated_at: 2024-02-20 08:40:34 UTC  
> Url: https://github.com/boostorg/charconv/pull/167#issuecomment-1953702690  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `21 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`0e3d9cf`)](https://app.codecov.io/gh/boostorg/charconv/commit/0e3d9cffd284cdc99b2c9bcb4553fd34b7c6825d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 92.30% compared to head [(`1a83f89`)](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 92.30%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/167/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #167   +/-   ##  
========================================  
  Coverage    92.30%   92.30%             
========================================  
  Files           64       64             
  Lines         8316     8316             
========================================  
  Hits          7676     7676             
  Misses         640      640             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/github\_issue\_152.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTUyLmNwcA==) | `98.78% <100.00%> (ø)` | |  
| [test/github\_issue\_152\_float128.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTUyX2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFycy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [...lude/boost/charconv/detail/dragonbox/dragonbox.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2RyYWdvbmJveC5ocHA=) | `97.38% <80.00%> (ø)` | |  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | `83.62% <0.00%> (ø)` | |  
| [...de/boost/charconv/detail/to\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvdG9fY2hhcnNfaW50ZWdlcl9pbXBsLmhwcA==) | `96.89% <25.00%> (ø)` | |  
| [include/boost/charconv/detail/dragonbox/floff.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2Zsb2ZmLmhwcA==) | `73.60% <50.00%> (ø)` | |  
| [src/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `92.59% <16.66%> (ø)` | |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `79.55% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0e3d9cf...1a83f89](https://app.codecov.io/gh/boostorg/charconv/pull/167?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
