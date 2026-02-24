# #179 url_view authority [Merged]

> Username: alandefreitas  
> Created at: 2022-05-17 20:42:24 UTC  
> Updated at: 2022-06-08 13:37:45 UTC  
> Merged at: 2022-05-19 00:17:03 UTC  
> Closed at: 2022-05-19 00:17:04 UTC  
> Url: https://github.com/boostorg/url/pull/179  

fix #171

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-18 00:53:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/179#pullrequestreview-976242957  

📁 include/boost/url/impl/url_view.ipp

```diff
 197 |+     a.port_number_ = port_number_;
 198 |+     a.host_type_ = host_type_;
 199 |+     return a;
```

> Username: vinniefalco  
> Created_at: 2022-05-18 00:53:27 UTC  
> Updated_at: 2022-05-18 00:53:28 UTC  
> Url: https://github.com/boostorg/url/pull/179#discussion_r875380266  

This function is kind of ugly, but as long as it works...

> Username: alandefreitas  
> Created_at: 2022-05-18 15:35:43 UTC  
> Updated_at: 2022-05-18 15:35:44 UTC  
> Url: https://github.com/boostorg/url/pull/179#discussion_r876051292  

Yes. I was trying to come up with a better intermediary between this and just `return parse_authority( encoded_authority() ).value();`. I couldn't think of anything that wouldn't parse the string again.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-18 00:53:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/179#pullrequestreview-976243074  

📁 include/boost/url/url_view.hpp

```diff
 627 |+ 
 628 |+         This function returns the authority as a
 629 |+         percent-encoded string.
```

> Username: vinniefalco  
> Created_at: 2022-05-18 00:53:43 UTC  
> Url: https://github.com/boostorg/url/pull/179#discussion_r875380365  

The doc is wrong


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-18 00:53:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/179#pullrequestreview-976243141  

📁 include/boost/url/url_view.hpp

```diff
 631 |+         @par Example
 632 |+         @code
 633 |+         assert( url_view( "http://www.example.com/index.htm" ).encoded_authority() == "www.example.com" );
```

> Username: vinniefalco  
> Created_at: 2022-05-18 00:53:53 UTC  
> Url: https://github.com/boostorg/url/pull/179#discussion_r875380422  

Wrong function in example


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-05-18 03:32:28 UTC  
> Updated_at: 2022-05-18 15:36:31 UTC  
> Url: https://github.com/boostorg/url/pull/179#issuecomment-1129529707  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/179?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#179](https://codecov.io/gh/CPPAlliance/url/pull/179?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (0297c87) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/9e3bc6c423afff0b4ee3d9553db8211037086a63?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (9e3bc6c) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 0297c87 differs from pull request most recent head ea4d3c6. Consider uploading reports for the commit ea4d3c6 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/179/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #179   +/-   ##  
========================================  
  Coverage    96.72%   96.73%             
========================================  
  Files          119      119             
  Lines         6047     6065   +18       
========================================  
+ Hits          5849     5867   +18       
  Misses         198      198             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/179/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/179/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/179/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/179?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/179?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [9e3bc6c...ea4d3c6](https://codecov.io/gh/CPPAlliance/url/pull/179?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-05-18 16:17:09 UTC  
> Url: https://github.com/boostorg/url/pull/179#issuecomment-1130221177  

LGTM

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-05-18 18:13:52 UTC  
> Url: https://github.com/boostorg/url/pull/179#issuecomment-1130347512  

Does "LGTM" also mean "merge it"?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-05-18 19:55:12 UTC  
> Url: https://github.com/boostorg/url/pull/179#issuecomment-1130475623  

Yes

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-05-18 19:55:23 UTC  
> Url: https://github.com/boostorg/url/pull/179#issuecomment-1130475770  

Don't forget to update the Help Card

---
