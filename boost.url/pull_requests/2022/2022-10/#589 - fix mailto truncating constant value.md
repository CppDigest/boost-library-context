# #589 fix mailto truncating constant value [Closed]

> Username: alandefreitas  
> Created at: 2022-10-10 20:32:06 UTC  
> Updated at: 2022-10-13 04:46:35 UTC  
> Closed at: 2022-10-11 00:10:47 UTC  
> Url: https://github.com/boostorg/url/pull/589  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-10 20:43:15 UTC  
> Url: https://github.com/boostorg/url/pull/589#issuecomment-1273802117  

GCOVR code coverage report [https://589.url.prtest.cppalliance.org/gcovr/index.html](https://589.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://589.url.prtest.cppalliance.org/genhtml/index.html](https://589.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-10 22:26:46 UTC  
> Updated_at: 2022-10-10 23:08:37 UTC  
> Url: https://github.com/boostorg/url/pull/589#issuecomment-1273872267  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/589?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#589](https://codecov.io/gh/boostorg/url/pull/589?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (eebc9cf) into [develop](https://codecov.io/gh/boostorg/url/commit/d05447f52521daba97c0d5142a5f2f20e804122e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d05447f) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/589/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/589?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #589   +/-   ##  
========================================  
  Coverage    96.81%   96.81%             
========================================  
  Files          139      139             
  Lines         6688     6688             
========================================  
  Hits          6475     6475             
  Misses         213      213             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/589?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/589?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d05447f...eebc9cf](https://codecov.io/gh/boostorg/url/pull/589?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-10 22:47:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/589#pullrequestreview-1136591929  

📁 example/mailto/mailto_grammar.hpp

```diff
  65 |+     static constexpr unsigned char c5 = char(14);
  66 |+     static constexpr unsigned char c6 = char(31);
  67 |+     static constexpr unsigned char c7 = char(127);
```

> Username: vinniefalco  
> Created_at: 2022-10-10 22:47:55 UTC  
> Url: https://github.com/boostorg/url/pull/589#discussion_r991685296  

this file should be renamed to one of: mailto_rfc.hpp, rfc6068.hpp, or rfc.hpp


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-10 23:03:26 UTC  
> Url: https://github.com/boostorg/url/pull/589#issuecomment-1273893672  

GCOVR code coverage report [https://589.url.prtest.cppalliance.org/gcovr/index.html](https://589.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://589.url.prtest.cppalliance.org/genhtml/index.html](https://589.url.prtest.cppalliance.org/genhtml/index.html)

---
