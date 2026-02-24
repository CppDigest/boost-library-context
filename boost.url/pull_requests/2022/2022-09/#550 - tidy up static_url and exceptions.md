# #550 tidy up static_url and exceptions [Closed]

> Username: vinniefalco  
> Created at: 2022-09-15 16:13:21 UTC  
> Updated at: 2022-09-15 17:52:35 UTC  
> Closed at: 2022-09-15 17:52:35 UTC  
> Url: https://github.com/boostorg/url/pull/550  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-15 16:55:05 UTC  
> Updated_at: 2022-09-15 17:10:59 UTC  
> Url: https://github.com/boostorg/url/pull/550#issuecomment-1248366154  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/550?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#550](https://codecov.io/gh/boostorg/url/pull/550?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ed998c7) into [develop](https://codecov.io/gh/boostorg/url/commit/89b006bbad156d5c79be12635c85d364260e4762?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (89b006b) will **decrease** coverage by `0.08%`.  
> The diff coverage is `73.33%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/550/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/550?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #550      +/-   ##  
===========================================  
- Coverage    96.70%   96.62%   -0.09%       
===========================================  
  Files          137      137                
  Lines         6618     6605      -13       
===========================================  
- Hits          6400     6382      -18       
- Misses         218      223       +5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/550?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/string\_token.hpp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9zdHJpbmdfdG9rZW4uaHBw) | `97.56% <0.00%> (ø)` | |  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `97.29% <0.00%> (+1.29%)` | :arrow_up: |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.73% <0.00%> (ø)` | |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `98.59% <ø> (ø)` | |  
| [include/boost/url/url.hpp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/impl/except.ipp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvZXhjZXB0LmlwcA==) | `100.00% <100.00%> (+18.75%)` | :arrow_up: |  
| [include/boost/url/impl/ipv4\_address.ipp](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9pcHY0X2FkZHJlc3MuaXBw) | `100.00% <100.00%> (ø)` | |  
| ... and [5 more](https://codecov.io/gh/boostorg/url/pull/550/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/550?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/550?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [89b006b...ed998c7](https://codecov.io/gh/boostorg/url/pull/550?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
