# #578 fix set_path_absolute [Closed]

> Username: alandefreitas  
> Created at: 2022-10-01 00:19:27 UTC  
> Updated at: 2022-10-13 04:50:23 UTC  
> Closed at: 2022-10-05 15:08:47 UTC  
> Url: https://github.com/boostorg/url/pull/578  

fix #390

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-01 00:29:34 UTC  
> Url: https://github.com/boostorg/url/pull/578#issuecomment-1264152885  

GCOVR code coverage report [https://578.url.prtest.cppalliance.org/gcovr/index.html](https://578.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://578.url.prtest.cppalliance.org/genhtml/index.html](https://578.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-01 00:35:41 UTC  
> Updated_at: 2022-10-01 00:38:05 UTC  
> Url: https://github.com/boostorg/url/pull/578#issuecomment-1264166566  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/578?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#578](https://codecov.io/gh/boostorg/url/pull/578?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (25b43af) into [develop](https://codecov.io/gh/boostorg/url/commit/6bd4691d4c52e97275846b85945da69115c0037d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6bd4691) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/578/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #578   +/-   ##  
========================================  
  Coverage    96.76%   96.76%             
========================================  
  Files          138      138             
  Lines         6700     6716   +16       
========================================  
+ Hits          6483     6499   +16       
  Misses         217      217             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/578/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.71% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/url/grammar/string\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/578/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9zdHJpbmdfdmlld19iYXNlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/578?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/578?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6bd4691...25b43af](https://codecov.io/gh/boostorg/url/pull/578?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-03 19:20:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/578#pullrequestreview-1128896301  

📁 test/unit/url.cpp

```diff
 293 |+             url u( "/kyle:xy" );
 294 |+             u.set_path_absolute( false );
 295 |+             BOOST_TEST_EQ( u.buffer(), "./kyle:xy" );
```

> Username: vinniefalco  
> Created_at: 2022-10-03 19:20:58 UTC  
> Url: https://github.com/boostorg/url/pull/578#discussion_r986140781  

I like breaking out individual tests for issues only after the initial release. Before then ,we can do what we want. But I will not hold up this PR for that.


---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-10-05 15:08:47 UTC  
> Url: https://github.com/boostorg/url/pull/578#issuecomment-1268576103  

Merged

---
