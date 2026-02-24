# #924 zone id setters and fix zone id round trips [Merged]

> Username: alandefreitas  
> Created at: 2025-08-29 00:14:35 UTC  
> Updated at: 2025-08-29 19:55:24 UTC  
> Merged at: 2025-08-29 17:01:50 UTC  
> Closed at: 2025-08-29 17:01:50 UTC  
> Url: https://github.com/boostorg/url/pull/924  

fix #919

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-08-29 00:17:46 UTC  
> Url: https://github.com/boostorg/url/pull/924#issuecomment-3235322432  

An automated preview of the documentation is available at [https://924.url.prtest2.cppalliance.org/index.html](https://924.url.prtest2.cppalliance.org/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-08-29 00:39:00 UTC  
> Url: https://github.com/boostorg/url/pull/924#issuecomment-3235352619  

An automated preview of the documentation is available at [https://924.url.prtest2.cppalliance.org/index.html](https://924.url.prtest2.cppalliance.org/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-08-29 01:09:08 UTC  
> Updated_at: 2025-08-29 17:01:59 UTC  
> Url: https://github.com/boostorg/url/pull/924#issuecomment-3235390614  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/924?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.22%. Comparing base ([`9b4c4b4`](https://app.codecov.io/gh/boostorg/url/commit/9b4c4b440e888cf4c43b14e0900db6b8499165ba?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`64cc28f`](https://app.codecov.io/gh/boostorg/url/commit/64cc28f2fcbade70e55b4e39afe5f0b8c1866aa3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/924/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop     #924    +/-   ##  
=========================================  
  Coverage    99.21%   99.22%              
=========================================  
  Files          158      158              
  Lines         8496     8598   +102       
=========================================  
+ Hits          8429     8531   +102       
  Misses          67       67              
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/924?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/static\_url.hpp](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fstatic_url.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url.hpp](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl_view_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [src/rfc/detail/ip\_literal\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&filepath=src%2Frfc%2Fdetail%2Fip_literal_rule.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9kZXRhaWwvaXBfbGl0ZXJhbF9ydWxlLmNwcA==) | `91.89% <ø> (ø)` | |  
| [src/url\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&filepath=src%2Furl_base.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3VybF9iYXNlLmNwcA==) | `99.66% <100.00%> (+0.02%)` | :arrow_up: |  
| [src/url\_view\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/924?src=pr&el=tree&filepath=src%2Furl_view_base.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3VybF92aWV3X2Jhc2UuY3Bw) | `96.93% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/924?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/924?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9b4c4b4...64cc28f](https://app.codecov.io/gh/boostorg/url/pull/924?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-08-29 02:07:31 UTC  
> Url: https://github.com/boostorg/url/pull/924#issuecomment-3235474364  

An automated preview of the documentation is available at [https://924.url.prtest2.cppalliance.org/index.html](https://924.url.prtest2.cppalliance.org/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-08-29 03:02:26 UTC  
> Url: https://github.com/boostorg/url/pull/924#issuecomment-3235555098  

An automated preview of the documentation is available at [https://924.url.prtest2.cppalliance.org/index.html](https://924.url.prtest2.cppalliance.org/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-08-29 05:02:29 UTC  
> Url: https://github.com/boostorg/url/pull/924#issuecomment-3235731801  

An automated preview of the documentation is available at [https://924.url.prtest2.cppalliance.org/index.html](https://924.url.prtest2.cppalliance.org/index.html)

---

## Comment 7

> Username: Ericson2314  
> Created_at: 2025-08-29 19:55:24 UTC  
> Url: https://github.com/boostorg/url/pull/924#issuecomment-3238097152  

Great!

---
