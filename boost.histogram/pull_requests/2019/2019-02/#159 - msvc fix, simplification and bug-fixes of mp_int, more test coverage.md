# #159 msvc fix, simplification and bug-fixes of mp_int, more test coverage [Merged]

> Username: HDembinski  
> Created at: 2019-02-18 08:31:09 UTC  
> Updated at: 2019-02-18 09:04:28 UTC  
> Merged at: 2019-02-18 09:04:28 UTC  
> Closed at: 2019-02-18 09:04:28 UTC  
> Url: https://github.com/boostorg/histogram/pull/159  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-02-18 08:38:45 UTC  
> Updated_at: 2019-02-18 08:41:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/159#issuecomment-464637316  

# [Codecov](https://codecov.io/gh/boostorg/histogram/pull/159?src=pr&el=h1) Report  
> Merging [#159](https://codecov.io/gh/boostorg/histogram/pull/159?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/histogram/commit/9abbe46e3dd57e2b55f7ab52eaed15994f76ae52?src=pr&el=desc) will **increase** coverage by `0.84%`.  
> The diff coverage is `98.19%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/histogram/pull/159/graphs/tree.svg?width=650&token=maq3UUcyCe&height=150&src=pr)](https://codecov.io/gh/boostorg/histogram/pull/159?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #159      +/-   ##  
===========================================  
+ Coverage    98.58%   99.42%   +0.84%       
===========================================  
  Files           33       33                
  Lines         1693     1732      +39       
===========================================  
+ Hits          1669     1722      +53       
+ Misses          24       10      -14  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/histogram/pull/159?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/histogram/indexed.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vaW5kZXhlZC5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/detail/meta.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vZGV0YWlsL21ldGEuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/axis/traits.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vYXhpcy90cmFpdHMuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/histogram.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vaGlzdG9ncmFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/serialization.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vc2VyaWFsaXphdGlvbi5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/detail/linearize.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vZGV0YWlsL2xpbmVhcml6ZS5ocHA=) | `100% <100%> (+3.38%)` | :arrow_up: |  
| [include/boost/histogram/axis/regular.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vYXhpcy9yZWd1bGFyLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/unlimited\_storage.hpp](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vdW5saW1pdGVkX3N0b3JhZ2UuaHBw) | `97.29% <97.67%> (+3.14%)` | :arrow_up: |  
| ... and [1 more](https://codecov.io/gh/boostorg/histogram/pull/159/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/histogram/pull/159?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/histogram/pull/159?src=pr&el=footer). Last update [9abbe46...456f59b](https://codecov.io/gh/boostorg/histogram/pull/159?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
