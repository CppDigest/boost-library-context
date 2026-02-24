# #507 fix magnet link example [Closed]

> Username: alandefreitas  
> Created at: 2022-09-05 23:12:00 UTC  
> Updated at: 2022-09-19 21:35:33 UTC  
> Closed at: 2022-09-06 21:08:26 UTC  
> Url: https://github.com/boostorg/url/pull/507  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-06 00:02:53 UTC  
> Updated_at: 2022-09-06 21:02:23 UTC  
> Url: https://github.com/boostorg/url/pull/507#issuecomment-1237534975  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/507?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#507](https://codecov.io/gh/CPPAlliance/url/pull/507?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (90ec854) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/81bb6547f81fbd71a71ccd6d0d4351ed0efa455e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (81bb654) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/507/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/507?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #507   +/-   ##  
========================================  
  Coverage    95.48%   95.48%             
========================================  
  Files          139      139             
  Lines         6601     6601             
========================================  
  Hits          6303     6303             
  Misses         298      298             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/507?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/param.hpp](https://codecov.io/gh/CPPAlliance/url/pull/507/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW0uaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/507?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/507?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [81bb654...90ec854](https://codecov.io/gh/CPPAlliance/url/pull/507?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
  
</details>

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-06 20:22:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/507#pullrequestreview-1098199640  

📁 include/boost/url/detail/config.hpp

```diff
 111 |+ #ifndef BOOST_URL_COW_STRINGS
 112 |+ #if defined(BOOST_LIBSTDCXX_VERSION) && (BOOST_LIBSTDCXX_VERSION < 60000 || (defined(_GLIBCXX_USE_CXX11_ABI) && _GLIBCXX_USE_CXX11_ABI == 0))
 113 |+ #define BOOST_URL_COW_STRING
```

> Username: vinniefalco  
> Created_at: 2022-09-06 20:22:42 UTC  
> Url: https://github.com/boostorg/url/pull/507#discussion_r964134398  

Spelling STRING / STRINGS


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-06 20:23:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/507#pullrequestreview-1098199961  

📁 include/boost/url/param.hpp

```diff
 134 |     {
 135 |-     #if BOOST_WORKAROUND(BOOST_GCC, < 50000)
 135 |+     #if BOOST_WORKAROUND(BOOST_GCC, < 60000)
```

> Username: vinniefalco  
> Created_at: 2022-09-06 20:23:01 UTC  
> Url: https://github.com/boostorg/url/pull/507#discussion_r964134605  

You aren't using `BOOST_URL_COW_STRING` here?


---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-09-06 21:08:26 UTC  
> Url: https://github.com/boostorg/url/pull/507#issuecomment-1238650765  

Merged

---
