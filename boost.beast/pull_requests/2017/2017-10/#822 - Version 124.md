# #822 Version 124 [Merged]

> Username: vinniefalco  
> Created at: 2017-10-21 01:41:18 UTC  
> Updated at: 2017-10-23 13:06:02 UTC  
> Merged at: 2017-10-22 22:24:30 UTC  
> Closed at: 2017-10-22 22:24:30 UTC  
> Url: https://github.com/boostorg/beast/pull/822  

API Changes:  
  
* http write returns success on connection close  
* Fix for a test matrix compiler  
* Use std::aligned_union  
* Fix basic_fields javadoc

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-10-21 03:09:57 UTC  
> Updated_at: 2017-10-22 17:42:56 UTC  
> Url: https://github.com/boostorg/beast/pull/822#issuecomment-338359877  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=h1) Report  
> Merging [#822](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/885b9dfe0b6bfc7be6a9158d60f0760aa43e748a?src=pr&el=desc) will **increase** coverage by `0.02%`.  
> The diff coverage is `0%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/822/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #822      +/-   ##  
===========================================  
+ Coverage    95.72%   95.74%   +0.02%       
===========================================  
  Files          105      105                
  Lines        10551    10539      -12       
===========================================  
- Hits         10100    10091       -9       
+ Misses         451      448       -3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `94.59% <0%> (+1.06%)` | :arrow_up: |  
| [include/boost/beast/http/serializer.hpp](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=footer). Last update [885b9df...9dc9ca1](https://codecov.io/gh/boostorg/beast/pull/822?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
