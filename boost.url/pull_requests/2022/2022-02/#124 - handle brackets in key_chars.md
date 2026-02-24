# #124 handle brackets in key_chars [Closed]

> Username: alandefreitas  
> Created at: 2022-02-14 23:53:31 UTC  
> Updated at: 2022-03-14 15:55:56 UTC  
> Closed at: 2022-03-11 23:31:56 UTC  
> Url: https://github.com/boostorg/url/pull/124  

Fixes #93 by supporting brackets in key_chars.  
  
The more generalized issue, including encoded chars for consumers, is now described in #123.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-02-15 04:57:06 UTC  
> Updated_at: 2022-03-11 20:29:04 UTC  
> Url: https://github.com/boostorg/url/pull/124#issuecomment-1039858739  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/124?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#124](https://codecov.io/gh/CPPAlliance/url/pull/124?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (aebfe9d) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/3cdcef3382168b27e78ed1b2943110aa69be5b08?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3cdcef3) will **decrease** coverage by `0.41%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head aebfe9d differs from pull request most recent head 1317ca8. Consider uploading reports for the commit 1317ca8 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/124/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/124?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #124      +/-   ##  
===========================================  
- Coverage    96.63%   96.22%   -0.42%       
===========================================  
  Files          109      104       -5       
  Lines         6071     5486     -585       
===========================================  
- Hits          5867     5279     -588       
- Misses         204      207       +3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/124?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/rfc/impl/query\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcXVlcnlfcnVsZS5pcHA=) | `93.47% <ø> (ø)` | |  
| [include/boost/url/impl/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaHBw) | `92.42% <0.00%> (-6.07%)` | :arrow_down: |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `93.16% <0.00%> (-3.11%)` | :arrow_down: |  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `97.92% <0.00%> (-0.31%)` | :arrow_down: |  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvY29uc3Rfc3RyaW5nLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/ipv4\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2NF9hZGRyZXNzLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/ipv6\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2Nl9hZGRyZXNzLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| ... and [19 more](https://codecov.io/gh/CPPAlliance/url/pull/124/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/124?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/124?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [3cdcef3...1317ca8](https://codecov.io/gh/CPPAlliance/url/pull/124?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---
