# #523 url constructor javadoc [Closed]

> Username: alandefreitas  
> Created at: 2022-09-09 18:03:37 UTC  
> Updated at: 2022-09-19 21:29:15 UTC  
> Closed at: 2022-09-15 19:49:25 UTC  
> Url: https://github.com/boostorg/url/pull/523  

fix #204

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-09 18:05:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/523#pullrequestreview-1102718031  

📁 include/boost/url/url.hpp

```diff
 123 |+         Strong guarantee.
 124 |+         Exceptions thrown on invalid input.
 125 |+         Calls to allocate may throw.
```

> Username: vinniefalco  
> Created_at: 2022-09-09 18:05:19 UTC  
> Url: https://github.com/boostorg/url/pull/523#discussion_r967343909  

I put them in this order  
```  
        Strong guarantee.  
        Calls to allocate may throw.  
        Exceptions thrown on invalid input.  
```


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-09-09 18:19:37 UTC  
> Url: https://github.com/boostorg/url/pull/523#issuecomment-1242319903  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/523?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#523](https://codecov.io/gh/CPPAlliance/url/pull/523?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (2f70ad7) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/65f3e0f0648e7ae02684d05994c6655d3668b994?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (65f3e0f) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/523/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/523?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #523   +/-   ##  
========================================  
  Coverage    96.77%   96.77%             
========================================  
  Files          137      137             
  Lines         6447     6447             
========================================  
  Hits          6239     6239             
  Misses         208      208             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/523?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/523/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/523?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/523?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [65f3e0f...2f70ad7](https://codecov.io/gh/CPPAlliance/url/pull/523?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
  
</details>

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-14 00:37:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/523#pullrequestreview-1106599572  

📁 include/boost/url/url.hpp

```diff
 126 |+ 
 127 |+         @throw system_error
 128 |+         The string contains an invalid <em>URI-reference</em>.
```

> Username: vinniefalco  
> Created_at: 2022-09-14 00:37:20 UTC  
> Url: https://github.com/boostorg/url/pull/523#discussion_r970192298  

these exceptions aren't correct

> Username: alandefreitas  
> Created_at: 2022-09-14 20:53:09 UTC  
> Updated_at: 2022-09-14 20:53:10 UTC  
> Url: https://github.com/boostorg/url/pull/523#discussion_r971286184  

`grammar::parse(s, uri_reference_rule).value()` can throw `system_error` and `url(url_view_base const& u)` can throw `std::length_error`. Is that incorrect?


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-09-14 21:09:30 UTC  
> Updated_at: 2022-09-14 21:11:27 UTC  
> Url: https://github.com/boostorg/url/pull/523#issuecomment-1247305247  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/523?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#523](https://codecov.io/gh/boostorg/url/pull/523?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a878f3f) into [develop](https://codecov.io/gh/boostorg/url/commit/89b006bbad156d5c79be12635c85d364260e4762?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (89b006b) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/523/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/523?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #523   +/-   ##  
========================================  
  Coverage    96.70%   96.70%             
========================================  
  Files          137      137             
  Lines         6618     6618             
========================================  
  Hits          6400     6400             
  Misses         218      218             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/523?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url.hpp](https://codecov.io/gh/boostorg/url/pull/523/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/523?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/523?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [89b006b...a878f3f](https://codecov.io/gh/boostorg/url/pull/523?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-09-15 17:00:44 UTC  
> Url: https://github.com/boostorg/url/pull/523#issuecomment-1248371513  

I might have updated these as well, please check my PR

---
