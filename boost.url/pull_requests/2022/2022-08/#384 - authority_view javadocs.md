# #384 authority_view javadocs [Closed]

> Username: alandefreitas  
> Created at: 2022-08-11 20:04:18 UTC  
> Updated at: 2022-08-30 21:04:18 UTC  
> Closed at: 2022-08-11 23:25:20 UTC  
> Url: https://github.com/boostorg/url/pull/384  

fix #354

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-11 20:20:12 UTC  
> Updated_at: 2022-08-11 23:05:03 UTC  
> Url: https://github.com/boostorg/url/pull/384#issuecomment-1212455709  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/384?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#384](https://codecov.io/gh/CPPAlliance/url/pull/384?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (5e17f7d) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/438d74c7fb85c604d9d8bec1c0a2c23aabd8710a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (438d74c) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 5e17f7d differs from pull request most recent head 274cfa6. Consider uploading reports for the commit 274cfa6 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/384/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/384?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #384   +/-   ##  
========================================  
  Coverage    97.89%   97.89%             
========================================  
  Files          131      131             
  Lines         6267     6267             
========================================  
  Hits          6135     6135             
  Misses         132      132             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/384?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/384/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `94.28% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/384/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/384?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/384?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [aec65e1...274cfa6](https://codecov.io/gh/CPPAlliance/url/pull/384?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-11 22:16:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/384#pullrequestreview-1070425376  

📁 include/boost/url/authority_view.hpp

```diff
 204 |+         This function returns the number of
 205 |+         characters in the authority, not
 206 |+         including any null terminator, if present.
```

> Username: vinniefalco  
> Created_at: 2022-08-11 22:16:44 UTC  
> Updated_at: 2022-08-11 22:16:45 UTC  
> Url: https://github.com/boostorg/url/pull/384#discussion_r943982989  

the authority view cannot promise a null terminator

> Username: alandefreitas  
> Created_at: 2022-08-11 22:47:06 UTC  
> Url: https://github.com/boostorg/url/pull/384#discussion_r943996624  

It's actually promising _NOT_ to include the null terminator. It might have a null terminator because it can now be constructed directly from a string.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-11 22:17:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/384#pullrequestreview-1070425740  

📁 include/boost/url/authority_view.hpp

```diff
 210 |+         authority_view a( "user:pass@www.example.com:8080" );
 211 |+ 
 212 |+         assert( u.size() == 30 );
```

> Username: vinniefalco  
> Created_at: 2022-08-11 22:17:15 UTC  
> Url: https://github.com/boostorg/url/pull/384#discussion_r943983276  

or...  
```  
assert( authority_view( "user:pass@www.example.com:8080" ).size() == 30 );  
```


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-11 22:17:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/384#pullrequestreview-1070425998  

📁 include/boost/url/authority_view.hpp

```diff
 230 |+         @code
 231 |+         authority_view a;
 232 |+         assert( a.empty() );
```

> Username: vinniefalco  
> Created_at: 2022-08-11 22:17:37 UTC  
> Url: https://github.com/boostorg/url/pull/384#discussion_r943983485  

or..  
```  
assert( authority_view( "" ).empty() );  
```


---
