# #567 consistent plus-to-space defaults [Closed]

> Username: alandefreitas  
> Created at: 2022-09-24 04:02:07 UTC  
> Updated at: 2022-12-22 16:25:36 UTC  
> Closed at: 2022-10-12 18:20:20 UTC  
> Url: https://github.com/boostorg/url/pull/567  

fix #237, fix #574

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-09-26 16:53:44 UTC  
> Url: https://github.com/boostorg/url/pull/567#issuecomment-1258341111  

GCOVR code coverage report [https://567.url.prtest.cppalliance.org/gcovr/index.html](https://567.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://567.url.prtest.cppalliance.org/genhtml/index.html](https://567.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-09-26 16:59:10 UTC  
> Updated_at: 2022-10-05 20:41:44 UTC  
> Url: https://github.com/boostorg/url/pull/567#issuecomment-1258346630  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/567?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#567](https://codecov.io/gh/boostorg/url/pull/567?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2b4bd01) into [develop](https://codecov.io/gh/boostorg/url/commit/ecdc8f1a5e6685c698a06be920e2ba3effb3c43a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ecdc8f1) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/567/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/567?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #567      +/-   ##  
===========================================  
+ Coverage    96.81%   96.83%   +0.01%       
===========================================  
  Files          139      139                
  Lines         6691     6693       +2       
===========================================  
+ Hits          6478     6481       +3       
+ Misses         213      212       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/567?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/567/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.11% <100.00%> (+0.09%)` | :arrow_up: |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/567/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/567?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/567?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ecdc8f1...2b4bd01](https://codecov.io/gh/boostorg/url/pull/567?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-05 15:13:38 UTC  
> Url: https://github.com/boostorg/url/pull/567#issuecomment-1268581935  

GCOVR code coverage report [https://567.url.prtest.cppalliance.org/gcovr/index.html](https://567.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://567.url.prtest.cppalliance.org/genhtml/index.html](https://567.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-10 23:27:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/567#pullrequestreview-1136610640  

📁 include/boost/url/impl/url_base.ipp

```diff
1150 |             opt,
1149 |-             detail::query_chars);
1151 |+             cs);
```

> Username: vinniefalco  
> Created_at: 2022-10-10 23:27:12 UTC  
> Url: https://github.com/boostorg/url/pull/567#discussion_r991699677  

Why the local variable?


---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-10-12 18:20:20 UTC  
> Updated_at: 2022-10-12 18:20:28 UTC  
> Url: https://github.com/boostorg/url/pull/567#issuecomment-1276564012  

https://github.com/vinniefalco/url/commits/query supersedes that

---
