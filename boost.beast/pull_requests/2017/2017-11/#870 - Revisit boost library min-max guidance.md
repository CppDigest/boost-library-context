# #870 Revisit boost library min/max guidance [Closed]

> Username: dstftw  
> Created at: 2017-11-05 20:49:33 UTC  
> Updated at: 2017-11-06 16:34:32 UTC  
> Closed at: 2017-11-06 16:34:31 UTC  
> Url: https://github.com/boostorg/beast/pull/870  

Currently, builds on Windows may fail if `Windows.h` is included before `beast` headers due to infamous `min` and `max` macros clashes with their `std` counterparts. To reproduce, just include `Windows.h` before `beast` headers in `beast\example\websocket\server\async\websocket_server_async.cpp` example. Compilation will fail with:  
```  
1>c:\dev\boost\boost\beast\websocket\detail\pmd_extension.hpp(280): error C2062: type 'unknown-type' unexpected  
1>c:\dev\boost\boost\beast\websocket\detail\pmd_extension.hpp(280): error C2059: syntax error: ')'  
1>c:\dev\boost\boost\beast\websocket\detail\pmd_extension.hpp(329): error C2589: '(': illegal token on right side of '::'  
1>c:\dev\boost\boost\beast\websocket\detail\pmd_extension.hpp(329): error C2062: type 'unknown-type' unexpected  
1>c:\dev\boost\boost\beast\websocket\detail\pmd_extension.hpp(329): error C2059: syntax error: ')'  
```  
Looks like not all occurrences of `std::min`/`std::max` has been fixed in #170 or maybe it's just a new code. This PR revisits and fixes the rest of them.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-11-05 20:55:40 UTC  
> Url: https://github.com/boostorg/beast/pull/870#issuecomment-342004747  

You got me! Thanks for the submission!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-11-05 22:35:33 UTC  
> Url: https://github.com/boostorg/beast/pull/870#issuecomment-342011666  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=h1) Report  
> Merging [#870](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/babb6bd59fd7110378859c5e941ab5b3dcf9a5bd?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/870/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #870   +/-   ##  
=======================================  
  Coverage     95.3%   95.3%             
=======================================  
  Files          104     104             
  Lines        10456   10456             
=======================================  
  Hits          9965    9965             
  Misses         491     491  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/read\_size.ipp](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvcmVhZF9zaXplLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [...ude/boost/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `94.38% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/flat\_buffer.ipp](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaXBw) | `99.5% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/multi\_buffer.ipp](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmlwcA==) | `98.62% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `92.45% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=footer). Last update [babb6bd...51d1e84](https://codecov.io/gh/boostorg/beast/pull/870?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-11-06 16:34:31 UTC  
> Url: https://github.com/boostorg/beast/pull/870#issuecomment-342205614  

Merged in 139, thanks!

---
