# #485 set_host with ip-literal [Closed]

> Username: alandefreitas  
> Created at: 2022-08-31 17:51:14 UTC  
> Updated at: 2022-09-19 21:35:37 UTC  
> Closed at: 2022-09-02 01:08:00 UTC  
> Url: https://github.com/boostorg/url/pull/485  

fix #483

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-08-31 22:47:37 UTC  
> Url: https://github.com/boostorg/url/pull/485#issuecomment-1233506901  

The only thing I can use from this is the tests, because my branch looks totally different in the files you changed

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:00:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/485#pullrequestreview-1092655837  

📁 include/boost/url/impl/url_base.ipp

```diff
 714 |+     // try ipv4
 715 |+     auto r = parse_ipv4_address(s);
 716 |+     if( r )
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:00:49 UTC  
> Url: https://github.com/boostorg/url/pull/485#discussion_r960134440  

N.B. I use `rv` as the canonical Result Value


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-09-02 00:51:50 UTC  
> Updated_at: 2022-09-02 00:53:46 UTC  
> Url: https://github.com/boostorg/url/pull/485#issuecomment-1234948655  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/485?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#485](https://codecov.io/gh/CPPAlliance/url/pull/485?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (5a6050a) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/7c5001966ee6bd32d1d6a4d1941d1f7202c661dc?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (7c50019) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/485/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/485?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #485   +/-   ##  
========================================  
  Coverage    94.47%   94.47%             
========================================  
  Files          137      137             
  Lines         6587     6587             
========================================  
  Hits          6223     6223             
  Misses         364      364             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/485?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/CPPAlliance/url/pull/485/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.61% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/485?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/485?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [7c50019...5a6050a](https://codecov.io/gh/CPPAlliance/url/pull/485?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
  
</details>

---
