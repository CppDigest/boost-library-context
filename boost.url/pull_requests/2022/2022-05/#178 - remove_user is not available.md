# #178 remove_user is not available [Merged]

> Username: alandefreitas  
> Created at: 2022-05-17 19:17:38 UTC  
> Updated at: 2022-06-08 13:37:41 UTC  
> Merged at: 2022-05-19 03:48:39 UTC  
> Closed at: 2022-05-19 03:48:39 UTC  
> Url: https://github.com/boostorg/url/pull/178  

fix #177

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-18 00:30:42 UTC  
> Updated_at: 2022-05-19 03:07:25 UTC  
> Url: https://github.com/boostorg/url/pull/178#issuecomment-1129445280  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/178?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#178](https://codecov.io/gh/CPPAlliance/url/pull/178?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (fb847f3) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/ce4ba198e12fff7eac6f968b0e151ea72867b350?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (ce4ba19) will **decrease** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head fb847f3 differs from pull request most recent head ae99737. Consider uploading reports for the commit ae99737 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/178/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/178?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #178      +/-   ##  
===========================================  
- Coverage    96.73%   96.72%   -0.02%       
===========================================  
  Files          119      119                
  Lines         6065     6038      -27       
===========================================  
- Hits          5867     5840      -27       
  Misses         198      198                
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/178?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/178/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `98.21% <ø> (-0.02%)` | :arrow_down: |  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/178/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/178/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/ipv6\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/178/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2Nl9hZGRyZXNzLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/178/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/178?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/178?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [ce4ba19...ae99737](https://codecov.io/gh/CPPAlliance/url/pull/178?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-05-18 16:18:15 UTC  
> Url: https://github.com/boostorg/url/pull/178#issuecomment-1130222242  

Commit message could read "remove_user is not available"

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-05-18 16:21:44 UTC  
> Url: https://github.com/boostorg/url/pull/178#issuecomment-1130225798  

LGTM but don't forget to update the Help Card if necessary.

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-05-18 18:15:54 UTC  
> Updated_at: 2022-05-18 18:16:07 UTC  
> Url: https://github.com/boostorg/url/pull/178#issuecomment-1130349288  

> Commit message could read "remove_user is not available"  
  
Yep. For any other function, it would be "remove X", but "remove remove_user" wouldn't look good. I also thought something like "remove_user is not available" could be ambiguous, as in "was it not available before or after this commit".

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-05-19 00:25:30 UTC  
> Url: https://github.com/boostorg/url/pull/178#issuecomment-1130816846  

> LGTM but don't forget to update the Help Card if necessary.  
  
The help card has "remove_user" :disappointed:

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-05-19 03:14:46 UTC  
> Url: https://github.com/boostorg/url/pull/178#issuecomment-1131106899  

An automated preview of the documentation is available at [https://178.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://178.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
