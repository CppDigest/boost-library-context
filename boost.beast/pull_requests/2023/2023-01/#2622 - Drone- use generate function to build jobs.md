# #2622 Drone: use generate function to build jobs [Merged]

> Username: sdarwin  
> Created at: 2023-01-12 21:31:03 UTC  
> Updated at: 2023-02-01 09:50:44 UTC  
> Merged at: 2023-02-01 09:50:43 UTC  
> Closed at: 2023-02-01 09:50:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2622  

Alan has abstracted the standard cpp.al drone jobs into a `generate` function that can be used on all repos, so adding a new compiler only needs to be done once in a central location.  
  
`generate` will probably be moved to the boost-ci repository eventually.  
  
Specific notes about beast: The beast drone file has two sets of jobs, the standard list similar to boostorg/json and also customized tasks that date back to Travis and include other packages. Some customized jobs are using the "build-and-test.sh" script. Currently they are kept and not discarded. If only new standard tests are relevant it's easy to delete the old ones.   
  
Since the `generate` function is purposely somewhat fixed in its specification, and isn't highly customizable, a few beast jobs are not passing and therefore remain in the custom list for the moment. They can be transitioned to the generate function over time.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-01-12 22:19:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2622#issuecomment-1381058274  

![pullrequest](https://2622.beast.tracing.cppalliance.org/pullrequest-bb3036da.png)  
Timeline tracing charts: [https://2622.beast.tracing.cppalliance.org/index.html](https://2622.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-01-13 19:04:43 UTC  
> Updated_at: 2023-01-16 20:13:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2622#issuecomment-1382260263  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2622](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (95f37c6) into [develop](https://codecov.io/gh/boostorg/beast/commit/97ece405b8127e1d4767a8f63b82478d5637b9ec?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (97ece40) will **decrease** coverage by `1.69%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2622/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2622      +/-   ##  
===========================================  
- Coverage    94.60%   92.91%   -1.70%       
===========================================  
  Files          154      177      +23       
  Lines        12052    13625    +1573       
===========================================  
+ Hits         11402    12659    +1257       
- Misses         650      966     +316       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/stream\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtX2Jhc2UuaHBw) | `26.31% <0.00%> (-73.69%)` | :arrow_down: |  
| [include/boost/beast/core/rate\_policy.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3JhdGVfcG9saWN5LmhwcA==) | `53.33% <0.00%> (-26.67%)` | :arrow_down: |  
| [include/boost/beast/core/impl/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYXN5bmNfYmFzZS5ocHA=) | `75.00% <0.00%> (-25.00%)` | :arrow_down: |  
| [include/boost/beast/core/buffers\_to\_string.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfdG9fc3RyaW5nLmhwcA==) | `87.50% <0.00%> (-12.50%)` | :arrow_down: |  
| [include/boost/beast/core/detail/flat\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9mbGF0X3N0cmVhbS5ocHA=) | `89.47% <0.00%> (-10.53%)` | :arrow_down: |  
| [include/boost/beast/http/impl/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmhwcA==) | `93.33% <0.00%> (-6.67%)` | :arrow_down: |  
| [include/boost/beast/core/detail/buffer\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXJfdHJhaXRzLmhwcA==) | `93.33% <0.00%> (-6.67%)` | :arrow_down: |  
| [include/boost/beast/core/impl/buffers\_cat.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19jYXQuaHBw) | `98.42% <0.00%> (-1.58%)` | :arrow_down: |  
| [include/boost/beast/http/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `90.74% <0.00%> (-1.44%)` | :arrow_down: |  
| [include/boost/beast/core/impl/buffers\_suffix.hpp](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19zdWZmaXguaHBw) | `98.57% <0.00%> (-1.43%)` | :arrow_down: |  
| ... and [71 more](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [97ece40...95f37c6](https://codecov.io/gh/boostorg/beast/pull/2622?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-01-16 14:43:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2622#issuecomment-1384166201  

![pullrequest](https://2622.beast.tracing.cppalliance.org/pullrequest-a991f8d6.png)  
Timeline tracing charts: [https://2622.beast.tracing.cppalliance.org/index.html](https://2622.beast.tracing.cppalliance.org/index.html)

---
