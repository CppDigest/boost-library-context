# #645 comparison differentiates absent and empty components [Merged]

> Username: alandefreitas  
> Created at: 2022-12-22 15:24:17 UTC  
> Updated at: 2022-12-22 20:12:07 UTC  
> Merged at: 2022-12-22 20:11:34 UTC  
> Closed at: 2022-12-22 20:11:34 UTC  
> Url: https://github.com/boostorg/url/pull/645  

This PR simplifies the URL comparison function. It also fixes a bug where the difference between some absent and empty components was not considered. This fix was part of #425, which is now obsoleted.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-22 15:39:54 UTC  
> Url: https://github.com/boostorg/url/pull/645#issuecomment-1362985648  

GCOVR code coverage report [https://645.url.prtest.cppalliance.org/gcovr/index.html](https://645.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://645.url.prtest.cppalliance.org/genhtml/index.html](https://645.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://645.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://645.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-12-22 15:41:02 UTC  
> Updated_at: 2022-12-22 19:09:58 UTC  
> Url: https://github.com/boostorg/url/pull/645#issuecomment-1362986776  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/645?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#645](https://codecov.io/gh/boostorg/url/pull/645?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3d783b6) into [develop](https://codecov.io/gh/boostorg/url/commit/cbcfa6a83f1ffe8e5b260ad925a259c3e24e6d8a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cbcfa6a) will **decrease** coverage by `0.09%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/645/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/645?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #645      +/-   ##  
===========================================  
- Coverage    96.48%   96.39%   -0.10%       
===========================================  
  Files          138      138                
  Lines         6746     6789      +43       
===========================================  
+ Hits          6509     6544      +35       
- Misses         237      245       +8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/645?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/boostorg/url/pull/645/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `93.93% <ø> (ø)` | |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/boostorg/url/pull/645/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `85.09% <100.00%> (+4.93%)` | :arrow_up: |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/645/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `96.68% <100.00%> (-1.28%)` | :arrow_down: |  
| [include/boost/url/detail/impl/normalize.ipp](https://codecov.io/gh/boostorg/url/pull/645/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvbm9ybWFsaXplLmlwcA==) | `90.84% <0.00%> (-1.24%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/645?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/645?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cbcfa6a...3d783b6](https://codecov.io/gh/boostorg/url/pull/645?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-22 16:34:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/645#pullrequestreview-1227938036  

📁 include/boost/url/authority_view.hpp

```diff
1279 |+         Linear in `min( a0.size(), a1.size() )`
1280 |+         @par Exception Safety
1281 |+         Throws nothing
```

> Username: vinniefalco  
> Created_at: 2022-12-22 16:34:31 UTC  
> Url: https://github.com/boostorg/url/pull/645#discussion_r1055640961  

No vertical space?


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-12-22 16:35:45 UTC  
> Url: https://github.com/boostorg/url/pull/645#issuecomment-1363068110  

Seems mostly ok except for style nits but what is the motivation for this?

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-12-22 16:45:46 UTC  
> Url: https://github.com/boostorg/url/pull/645#issuecomment-1363087154  

> Seems mostly ok except for style nits  
  
Great. I'll fix that.  
  
> but what is the motivation for this?  
  
The main motivation is fixing the bug. The previous function would compare an absent component and an empty component as the same thing, but that's wrong.  
  
```cpp  
BOOST_TEST_EQ(url("https:"), url("https://"));  
BOOST_TEST_EQ(url("https://@www.boost.org"), url("https://www.boost.org"));  
BOOST_TEST_EQ(url("https:"), url("https:?"));  
BOOST_TEST_EQ(url("https:"), url("https:#"));  
// ... etc  
```  
  
should fail.  
  
I'll improve coverage with a few more tests and the difference is going to be more explicit.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-12-22 16:48:11 UTC  
> Url: https://github.com/boostorg/url/pull/645#issuecomment-1363089586  

"simplify URL comparison" doesnt' really tell me that there's a bug in the current release of Boost.URL

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-12-22 17:11:24 UTC  
> Url: https://github.com/boostorg/url/pull/645#issuecomment-1363114534  

> "simplify URL comparison" doesnt' really tell me that there's a bug in the current release of Boost.URL  
  
Yes. I chose that because it's not the only thing happening in the PR and because of the trend of "refactor xx" being a synonym for "fix xx".

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-12-22 19:06:26 UTC  
> Url: https://github.com/boostorg/url/pull/645#issuecomment-1363250466  

GCOVR code coverage report [https://645.url.prtest.cppalliance.org/gcovr/index.html](https://645.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://645.url.prtest.cppalliance.org/genhtml/index.html](https://645.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://645.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://645.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
