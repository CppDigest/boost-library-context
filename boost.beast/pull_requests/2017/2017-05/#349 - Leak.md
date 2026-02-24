# #349 Leak [Closed]

> Username: vinniefalco  
> Created at: 2017-05-09 19:45:29 UTC  
> Updated at: 2017-05-09 21:43:46 UTC  
> Closed at: 2017-05-09 21:43:46 UTC  
> Url: https://github.com/boostorg/beast/pull/349  

Testing a suppression for openssl

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-05-09 20:24:45 UTC  
> Url: https://github.com/boostorg/beast/pull/349#issuecomment-300290523  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=h1) Report  
> Merging [#349](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/dd02097d265857c3cfca435317f06f7af701a012?src=pr&el=desc) will **decrease** coverage by `7.36%`.  
> The diff coverage is `87.65%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/349/graphs/tree.svg?src=pr&width=650&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #349      +/-   ##  
==========================================  
- Coverage   93.28%   85.91%   -7.37%       
==========================================  
  Files          95       95                
  Lines        6503     6829     +326       
==========================================  
- Hits         6066     5867     -199       
- Misses        437      962     +525  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `58.33% <ø> (-41.67%)` | :arrow_down: |  
| [include/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `83.72% <ø> (-16.28%)` | :arrow_down: |  
| [include/beast/websocket/impl/ssl.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zc2wuaXBw) | `71.42% <ø> (+71.42%)` | :arrow_up: |  
| [include/beast/core/detail/sync\_ostream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9zeW5jX29zdHJlYW0uaHBw) | `83.33% <ø> (-16.67%)` | :arrow_down: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `26.31% <ø> (-73.69%)` | :arrow_down: |  
| [include/beast/core/detail/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9zdGF0aWNfc3RyaW5nLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/ci\_char\_traits.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9jaV9jaGFyX3RyYWl0cy5ocHA=) | `63.63% <ø> (-36.37%)` | :arrow_down: |  
| [include/beast/core/buffers\_adapter.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcnNfYWRhcHRlci5ocHA=) | `0% <ø> (ø)` | |  
| [include/beast/websocket/detail/frame.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `91.93% <ø> (-1.62%)` | :arrow_down: |  
| [include/beast/websocket/detail/hybi13.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2h5YmkxMy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| ... and [129 more](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=footer). Last update [dd02097...24586db](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-05-09 20:24:45 UTC  
> Updated_at: 2017-05-09 21:15:18 UTC  
> Url: https://github.com/boostorg/beast/pull/349#issuecomment-300290524  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=h1) Report  
> Merging [#349](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/dd02097d265857c3cfca435317f06f7af701a012?src=pr&el=desc) will **decrease** coverage by `7.36%`.  
> The diff coverage is `87.65%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/349/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #349      +/-   ##  
==========================================  
- Coverage   93.28%   85.91%   -7.37%       
==========================================  
  Files          95       95                
  Lines        6503     6829     +326       
==========================================  
- Hits         6066     5867     -199       
- Misses        437      962     +525  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `41.66% <ø> (ø)` | |  
| [include/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `98.82% <ø> (-1.18%)` | :arrow_down: |  
| [include/beast/core/detail/sync\_ostream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9zeW5jX29zdHJlYW0uaHBw) | `83.33% <ø> (-16.67%)` | :arrow_down: |  
| [include/beast/websocket/detail/frame.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `91.93% <ø> (-1.62%)` | :arrow_down: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `50% <ø> (-50%)` | :arrow_down: |  
| [include/beast/http/concepts.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2NvbmNlcHRzLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9zdGF0aWNfc3RyaW5nLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/consuming\_buffers.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvY29uc3VtaW5nX2J1ZmZlcnMuaXBw) | `82.6% <ø> (-17.4%)` | :arrow_down: |  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `89.33% <ø> (+1.83%)` | :arrow_up: |  
| [include/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `83.72% <ø> (-16.28%)` | :arrow_down: |  
| ... and [129 more](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=footer). Last update [dd02097...1e0b12b](https://codecov.io/gh/vinniefalco/Beast/pull/349?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
