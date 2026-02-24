# #570 simplify decode_view ops [Closed]

> Username: alandefreitas  
> Created at: 2022-09-27 01:01:18 UTC  
> Updated at: 2022-10-13 04:48:26 UTC  
> Closed at: 2022-10-06 02:56:57 UTC  
> Url: https://github.com/boostorg/url/pull/570  

fix #484

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-09-27 01:13:49 UTC  
> Url: https://github.com/boostorg/url/pull/570#issuecomment-1258843029  

GCOVR code coverage report [https://570.url.prtest.cppalliance.org/gcovr/index.html](https://570.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://570.url.prtest.cppalliance.org/genhtml/index.html](https://570.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-09-27 01:16:38 UTC  
> Updated_at: 2022-10-05 16:06:00 UTC  
> Url: https://github.com/boostorg/url/pull/570#issuecomment-1258844453  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/570?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#570](https://codecov.io/gh/boostorg/url/pull/570?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1aedecd) into [develop](https://codecov.io/gh/boostorg/url/commit/ecdc8f1a5e6685c698a06be920e2ba3effb3c43a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ecdc8f1) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/570/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/570?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #570      +/-   ##  
===========================================  
- Coverage    96.81%   96.81%   -0.01%       
===========================================  
  Files          139      139                
  Lines         6691     6683       -8       
===========================================  
- Hits          6478     6470       -8       
  Misses         213      213                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/570?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/string\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/570/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9zdHJpbmdfdmlld19iYXNlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/pct\_string\_view.hpp](https://codecov.io/gh/boostorg/url/pull/570/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/decode\_view.hpp](https://codecov.io/gh/boostorg/url/pull/570/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/570?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/570?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ecdc8f1...1aedecd](https://codecov.io/gh/boostorg/url/pull/570?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-09-27 01:28:26 UTC  
> Url: https://github.com/boostorg/url/pull/570#issuecomment-1258850427  

GCOVR code coverage report [https://570.url.prtest.cppalliance.org/gcovr/index.html](https://570.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://570.url.prtest.cppalliance.org/genhtml/index.html](https://570.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-05 15:18:27 UTC  
> Url: https://github.com/boostorg/url/pull/570#issuecomment-1268587210  

GCOVR code coverage report [https://570.url.prtest.cppalliance.org/gcovr/index.html](https://570.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://570.url.prtest.cppalliance.org/genhtml/index.html](https://570.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-10-05 19:58:09 UTC  
> Url: https://github.com/boostorg/url/pull/570#issuecomment-1268902929  

if you like it, merge it

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-10-06 02:56:57 UTC  
> Url: https://github.com/boostorg/url/pull/570#issuecomment-1269239779  

Merged

---
