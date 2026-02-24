# #898 Version 146 [Merged]

> Username: vinniefalco  
> Created at: 2017-11-21 23:16:59 UTC  
> Updated at: 2017-12-02 20:12:25 UTC  
> Merged at: 2017-11-26 21:15:53 UTC  
> Closed at: 2017-11-26 21:15:53 UTC  
> Url: https://github.com/boostorg/beast/pull/898  

* Fix some typos  
* Faster ascii_tolower  
* Documentation tidying  
* Fix typo in examples documentation  
* Add detail::aligned_union and tidy up  
* Use variant in buffers_cat_view  
  
API Changes:  
  
* Remove unintended public members of handler_ptr

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-22 21:22:32 UTC  
> Updated_at: 2017-11-26 20:40:45 UTC  
> Url: https://github.com/boostorg/beast/pull/898#issuecomment-346476774  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=h1) Report  
> Merging [#898](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6e6e1f6fbc93b92c8b21d673933e3b77951b4fa0?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `96.24%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/898/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #898      +/-   ##  
===========================================  
- Coverage    95.69%   95.68%   -0.02%       
===========================================  
  Files          104      104                
  Lines        10454    10441      -13       
===========================================  
- Hits         10004     9990      -14       
- Misses         450      451       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/handler\_ptr.hpp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2hhbmRsZXJfcHRyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/type\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.ipp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/variant.hpp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC92YXJpYW50LmhwcA==) | `100% <100%> (+4.47%)` | :arrow_up: |  
| [include/boost/beast/core/string.hpp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/buffers\_cat.ipp](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19jYXQuaXBw) | `95.16% <91.07%> (-3.71%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=footer). Last update [6e6e1f6...5d83c79](https://codecov.io/gh/boostorg/beast/pull/898?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
