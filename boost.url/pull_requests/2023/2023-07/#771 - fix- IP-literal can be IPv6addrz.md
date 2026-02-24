# #771 fix: IP-literal can be IPv6addrz [Merged]

> Username: alandefreitas  
> Created at: 2023-07-19 17:17:46 UTC  
> Updated at: 2024-02-21 00:55:46 UTC  
> Merged at: 2023-07-20 17:13:32 UTC  
> Closed at: 2023-07-20 17:13:32 UTC  
> Url: https://github.com/boostorg/url/pull/771  

This commit changes the IP-literal rule to also accept IPv6addrz as a valid ipv6 host type. IPv6addrz includes a ZoneID at the end, delimited by an encoded "%25". The ipv6_address class is unmodified, as the mapping from the ZoneID to a std::uint32_t is dependent on the application context. The original ZoneID can be obtained from the url_view.  
  
fix #711

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-07-19 17:41:07 UTC  
> Updated_at: 2023-07-20 16:18:15 UTC  
> Url: https://github.com/boostorg/url/pull/771#issuecomment-1642494859  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#771](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c01b82c) into [develop](https://app.codecov.io/gh/boostorg/url/commit/cac7c200e28e3559d4ea9ea43033bda5e8f66c39?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cac7c20) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/771/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #771      +/-   ##  
===========================================  
+ Coverage    97.26%   97.27%   +0.01%       
===========================================  
  Files          155      156       +1       
  Lines         8518     8565      +47       
===========================================  
+ Hits          8285     8332      +47       
  Misses         233      233                
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_view\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [src/rfc/detail/ip\_literal\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9kZXRhaWwvaXBfbGl0ZXJhbF9ydWxlLmNwcA==) | `91.89% <100.00%> (+3.00%)` | :arrow_up: |  
| [src/rfc/detail/ipv6\_addrz\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9kZXRhaWwvaXB2Nl9hZGRyel9ydWxlLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [src/url\_view\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3VybF92aWV3X2Jhc2UuY3Bw) | `96.93% <100.00%> (+0.11%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cac7c20...c01b82c](https://app.codecov.io/gh/boostorg/url/pull/771?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-07-19 17:41:14 UTC  
> Url: https://github.com/boostorg/url/pull/771#issuecomment-1642495040  

GCOVR code coverage report [https://771.url.prtest2.cppalliance.org/gcovr/index.html](https://771.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://771.url.prtest2.cppalliance.org/genhtml/index.html](https://771.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://771.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://771.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-07-20 14:21:01 UTC  
> Url: https://github.com/boostorg/url/pull/771#issuecomment-1644017113  

GCOVR code coverage report [https://771.url.prtest2.cppalliance.org/gcovr/index.html](https://771.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://771.url.prtest2.cppalliance.org/genhtml/index.html](https://771.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://771.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://771.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-07-20 16:16:29 UTC  
> Url: https://github.com/boostorg/url/pull/771#issuecomment-1644213933  

GCOVR code coverage report [https://771.url.prtest2.cppalliance.org/gcovr/index.html](https://771.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://771.url.prtest2.cppalliance.org/genhtml/index.html](https://771.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://771.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://771.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
