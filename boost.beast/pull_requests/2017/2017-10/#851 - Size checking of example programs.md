# #851 Size checking of example programs [Closed]

> Username: djarek  
> Created at: 2017-10-28 21:30:11 UTC  
> Updated at: 2019-09-10 21:02:42 UTC  
> Closed at: 2018-05-01 17:32:31 UTC  
> Url: https://github.com/boostorg/beast/pull/851  

Added a script which checks the example programs binary size (both with symbols and stripped) and outputs the statistics of symbols which contribute the most to the size of the stripped binary.  
  
Resolves: #850

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-10-28 21:46:46 UTC  
> Updated_at: 2017-11-18 23:24:42 UTC  
> Url: https://github.com/boostorg/beast/pull/851#issuecomment-340221941  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=h1) Report  
> Merging [#851](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/e8e340d0980c85fbd9d77832283ace6186214e6f?src=pr&el=desc) will **increase** coverage by `0.36%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/851/graphs/tree.svg?height=150&src=pr&token=Gq6MFA9JRF&width=650)](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #851      +/-   ##  
===========================================  
+ Coverage     95.3%   95.66%   +0.36%       
===========================================  
  Files          104      104                
  Lines        10456    10453       -3       
===========================================  
+ Hits          9965    10000      +35       
+ Misses         491      453      -38  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/message.hpp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `92.37% <100%> (-0.09%)` | :arrow_down: |  
| [include/boost/beast/core/impl/multi\_buffer.ipp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmlwcA==) | `98.62% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `80.48% <100%> (-0.47%)` | :arrow_down: |  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `95.63% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/flat\_buffer.ipp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaXBw) | `99.5% <100%> (ø)` | :arrow_up: |  
| [...ude/boost/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `94.38% <100%> (ø)` | :arrow_up: |  
| ... and [2 more](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=footer). Last update [e8e340d...5aa9d78](https://codecov.io/gh/boostorg/beast/pull/851?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-10-28 23:33:25 UTC  
> Url: https://github.com/boostorg/beast/pull/851#issuecomment-340226712  

Maybe we should just leave out the link time optimization setting?

---

## Comment 3

> Username: djarek  
> Created_at: 2017-10-29 12:59:53 UTC  
> Url: https://github.com/boostorg/beast/pull/851#issuecomment-340260657  

Done.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-11-23 02:40:10 UTC  
> Url: https://github.com/boostorg/beast/pull/851#issuecomment-346520984  

How do I see the results?

---

## Comment 5

> Username: stale[bot]  
> Created_at: 2018-04-24 16:52:20 UTC  
> Url: https://github.com/boostorg/beast/pull/851#issuecomment-384003477  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created_at: 2018-05-01 17:32:25 UTC  
> Url: https://github.com/boostorg/beast/pull/851#issuecomment-385733353  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---
