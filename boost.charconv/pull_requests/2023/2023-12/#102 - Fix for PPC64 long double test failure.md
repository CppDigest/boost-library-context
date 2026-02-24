# #102 Fix for PPC64 long double test failure [Merged]

> Username: mborland  
> Created at: 2023-12-07 09:15:19 UTC  
> Updated at: 2023-12-07 09:59:16 UTC  
> Merged at: 2023-12-07 09:59:11 UTC  
> Closed at: 2023-12-07 09:59:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/102  

Tested on Fedora 39 with GCC 13.1.1  
See: https://developers.redhat.com/articles/2023/05/16/benefits-fedora-38-long-double-transition-ppc64le

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-07 09:47:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/102#issuecomment-1845019677  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
> Merging [#102](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) (f9ee9f5) into [develop](https://app.codecov.io/gh/cppalliance/charconv/commit/33e4cc56cae2961eb530cc9bed042168aac5eb9f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) (33e4cc5) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/102/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #102   +/-   ##  
========================================  
  Coverage    75.90%   75.90%             
========================================  
  Files           28       28             
  Lines         3955     3955             
========================================  
  Hits          3002     3002             
  Misses         953      953             
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | `82.02% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [33e4cc5...f9ee9f5](https://app.codecov.io/gh/cppalliance/charconv/pull/102?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
