# #1438 Resolve issue #1435 and make tests and examples buildable with std::string_view on MSVC v141 15.9.6 [Closed]

> Username: dsewtz  
> Created at: 2019-02-08 00:07:08 UTC  
> Updated at: 2019-02-08 02:54:46 UTC  
> Closed at: 2019-02-08 02:54:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1438  

FIXED debug assertion in MSVC v141 15.9.6 with /std:c++17 and BOOST_BEAST_USE_STD_STRING_VIEW  
from iterator debug helpers in rfc7230.ipp for dereferencing end-iterator:  
(can't test on other compilers right now, sorry)  
  
example and test can be build on msvc v141 15.9.6 using /std:c++17 and BOOST_BEAST_USE_STD_STRING_VIEW. changed string_view.to_string() to std:string(string_view), awaiting availability of Library Fundamentals TS here.  
  
reactivated relevant tests to param_list  
#ifdef 0 test exhibited same assertion failed error in debug mode.  
Now fixed in DEBUG on msvc v141 15.9.6 with BOOST_BEAST_USE_STD_STRING_VIEW and /std:c++17.  
Still 2 failures in RELEASE : ce(";xy"); ce(";x;y");  
  
Looked up http paramters (transfer-encoding, etc.) and changed tests as well as misleading comment to match specs.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-02-08 00:27:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1438#issuecomment-461646973  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1438?src=pr&el=h1) Report  
> Merging [#1438](https://codecov.io/gh/boostorg/beast/pull/1438?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/5a7a1a3f6c140ce7059b96d2f982e10414c51e7f?src=pr&el=desc) will **increase** coverage by `0.21%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1438/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1438?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1438      +/-   ##  
===========================================  
+ Coverage    93.51%   93.72%   +0.21%       
===========================================  
  Files          130      130                
  Lines        11377    11387      +10       
===========================================  
+ Hits         10639    10673      +34       
+ Misses         738      714      -24  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1438?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/rfc7230.ipp](https://codecov.io/gh/boostorg/beast/pull/1438/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmZjNzIzMC5pcHA=) | `100% <100%> (+5.61%)` | :arrow_up: |  
| [include/boost/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/boostorg/beast/pull/1438/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `98.24% <0%> (+12.28%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1438?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1438?src=pr&el=footer). Last update [5a7a1a3...f66e9f7](https://codecov.io/gh/boostorg/beast/pull/1438?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
