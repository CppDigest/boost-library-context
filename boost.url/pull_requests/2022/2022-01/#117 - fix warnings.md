# #117 fix warnings [Closed]

> Username: alandefreitas  
> Created at: 2022-01-24 21:56:12 UTC  
> Updated at: 2022-03-14 15:56:45 UTC  
> Closed at: 2022-01-26 23:43:54 UTC  
> Url: https://github.com/boostorg/url/pull/117  

Fixes warnings such as  
  
```  
boost/libs/url/include/boost/url/authority_view.hpp:118:5: warning: enclosing class of ‘constexpr’ non-static member function ‘boost::urls::pos_t boost::urls::authority_view::offset(int) const’ is not a literal type  
  118 |     offset(int id) const noexcept ->  
      |     ^~~~~~  
boost/libs/url/include/boost/url/authority_view.hpp:75:5: note: ‘boost::urls::authority_view’ is not literal because:  
   75 |     authority_view  
      |     ^~~~~~~~~~~~~~  
boost/libs/url/include/boost/url/authority_view.hpp:75:5: note:   ‘boost::urls::authority_view’ has a non-trivial destructor  
```  
  
```  
boost/libs/url/test/unit/url_view.cpp:621:25: warning: default argument specified for lambda parameter [-Wpedantic]  
  621 |             char const* encoded = nullptr,  
      |             ~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-01-25 04:41:41 UTC  
> Updated_at: 2022-01-26 22:50:40 UTC  
> Url: https://github.com/boostorg/url/pull/117#issuecomment-1020805991  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/117?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#117](https://codecov.io/gh/CPPAlliance/url/pull/117?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (4a84194) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/b1c9a7f27c1e35b46cee66f950d6db24f91c3773?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (b1c9a7f) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/117/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #117   +/-   ##  
========================================  
  Coverage    96.28%   96.28%             
========================================  
  Files          104      104             
  Lines         5458     5458             
========================================  
  Hits          5255     5255             
  Misses         203      203             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/117?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/117?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [b1c9a7f...4a84194](https://codecov.io/gh/CPPAlliance/url/pull/117?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-25 17:08:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/117#pullrequestreview-862607404  

📁 test/unit/pct_encoding.cpp

```diff
 363 |             []( string_view s,
 364 |                 string_view m0,
 365 |-                 bool space_to_plus = false)
```

> Username: vinniefalco  
> Created_at: 2022-01-25 17:08:30 UTC  
> Url: https://github.com/boostorg/url/pull/117#discussion_r791939046  

why did we remove the default argument?

> Username: alandefreitas  
> Created_at: 2022-01-25 18:54:49 UTC  
> Updated_at: 2022-01-25 18:54:50 UTC  
> Url: https://github.com/boostorg/url/pull/117#discussion_r792026112  

```  
warning: default argument specified for lambda parameter  
```

> Username: vinniefalco  
> Created_at: 2022-01-26 16:12:38 UTC  
> Url: https://github.com/boostorg/url/pull/117#discussion_r792803338  

can we just make this a separate function, to keep the default?

> Username: alandefreitas  
> Created_at: 2022-01-26 19:47:43 UTC  
> Url: https://github.com/boostorg/url/pull/117#discussion_r792992164  

Sure. It's not that nice to have long lambdas anyway.


---
