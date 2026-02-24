# #877 Version 140 [Merged]

> Username: vinniefalco  
> Created at: 2017-11-09 04:13:57 UTC  
> Updated at: 2017-11-12 22:43:45 UTC  
> Merged at: 2017-11-09 09:09:33 UTC  
> Closed at: 2017-11-09 09:09:33 UTC  
> Url: https://github.com/boostorg/beast/pull/877  

* Fix some integer warnings in 64-bit builds  
* Fix utf8_checker test failures  
* Fix signature for async_read_some, and tests  
* Tidy up basic_parser  
* Some basic_fields special members are protected

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-09 05:33:05 UTC  
> Url: https://github.com/boostorg/beast/pull/877#issuecomment-343051888  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=h1) Report  
> Merging [#877](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6eba0e8f9ef81bf38285a7eee260078684611e2b?src=pr&el=desc) will **increase** coverage by `0.36%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/877/graphs/tree.svg?height=150&src=pr&width=650&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #877      +/-   ##  
===========================================  
+ Coverage     95.3%   95.66%   +0.36%       
===========================================  
  Files          104      104                
  Lines        10456    10453       -3       
===========================================  
+ Hits          9965    10000      +35       
+ Misses         491      453      -38  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `92.37% <ø> (-0.09%)` | :arrow_down: |  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `80.48% <100%> (-0.47%)` | :arrow_down: |  
| [include/boost/beast/http/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `95.63% <100%> (ø)` | :arrow_up: |  
| [...lude/boost/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `99.23% <0%> (+29%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=footer). Last update [6eba0e8...e2d9b0d](https://codecov.io/gh/boostorg/beast/pull/877?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
