# #957 Version 151 [Merged]

> Username: vinniefalco  
> Created at: 2017-12-30 15:49:40 UTC  
> Updated at: 2018-01-25 22:07:55 UTC  
> Merged at: 2017-12-31 17:41:09 UTC  
> Closed at: 2017-12-31 17:41:09 UTC  
> Url: https://github.com/boostorg/beast/pull/957  

* Sanitizer failures are errors  
* Depend on container_hash  
* Fix high-ASCII in source file  
  
WebSocket:  
  
* Control callback is invoked on the execution context  
* Add stream_fwd.hpp  
* Remove unnecessary include  
  
API Changes:  
  
* http::parser is not MoveConstructible  
* permessage-deflate is a compile-time feature

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-12-30 16:18:09 UTC  
> Updated_at: 2017-12-31 06:49:43 UTC  
> Url: https://github.com/boostorg/beast/pull/957#issuecomment-354554327  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/957?src=pr&el=h1) Report  
> Merging [#957](https://codecov.io/gh/boostorg/beast/pull/957?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/617fc428799bf3684c996d500068dbeacd8f5877?src=pr&el=desc) will **decrease** coverage by `0.14%`.  
> The diff coverage is `91.22%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/957/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/957?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #957      +/-   ##  
===========================================  
- Coverage    95.69%   95.54%   -0.15%       
===========================================  
  Files          104      105       +1       
  Lines        10441    10543     +102       
===========================================  
+ Hits          9991    10073      +82       
- Misses         450      470      +20  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/957?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/base64.hpp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iYXNlNjQuaHBw) | `98.61% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/detail/frame.hpp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `83.01% <ø> (ø)` | :arrow_up: |  
| [...ude/boost/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `98.5% <ø> (+4.12%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.5% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.ipp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [...clude/boost/beast/websocket/detail/stream\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3N0cmVhbV9iYXNlLmhwcA==) | `63.63% <63.63%> (ø)` | |  
| ... and [4 more](https://codecov.io/gh/boostorg/beast/pull/957/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/957?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/957?src=pr&el=footer). Last update [617fc42...6f08814](https://codecov.io/gh/boostorg/beast/pull/957?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: glenfe  
> Created_at: 2017-12-30 16:58:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/957#pullrequestreview-86033091  

📁 CMakeLists.txt

```diff
 154 |-         set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fsanitize=address,undefined")
 153 |+           "${CMAKE_CXX_FLAGS} -DBOOST_BEAST_NO_SLOW_TESTS=1 -msse4.2 -funsigned-char -fno-omit-frame-pointer -fsanitize=address,undefined -fno-sanitize-recover=address,undefined -fsanitize-blacklist=${PROJECT_SOURCE_DIR}/tools/blacklist.supp")
 154 |+         set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fsanitize=address,undefined -fno-sanitize-recover=address,undefined")
```

> Username: glenfe  
> Created_at: 2017-12-30 16:48:35 UTC  
> Updated_at: 2017-12-30 21:23:51 UTC  
> Url: https://github.com/boostorg/beast/pull/957#discussion_r159125152  

Are there any drawbacks to having just `-f-no-sanitize-recover=all` ?

> Username: vinniefalco  
> Created_at: 2017-12-30 17:02:04 UTC  
> Updated_at: 2017-12-30 21:23:51 UTC  
> Url: https://github.com/boostorg/beast/pull/957#discussion_r159125330  

I have no idea I just copy pasted that


📁 include/boost/beast/http/parser.hpp

```diff
  96 |-     /** Constructor
  93 |+     /// Constructor (disallowed)
  94 |+     parser(parser&& other) = delete;
```

> Username: glenfe  
> Created_at: 2017-12-30 16:49:44 UTC  
> Updated_at: 2017-12-30 21:23:51 UTC  
> Url: https://github.com/boostorg/beast/pull/957#discussion_r159125172  

No need for `parser(parser&& other) = delete;` since you explicitly delete the copy constructor. e.g. You're not deleting the move-assignment operator since you're letting the explicit deletion of the copy-assignment operator imply that.

> Username: vinniefalco  
> Created_at: 2017-12-30 17:01:38 UTC  
> Updated_at: 2017-12-30 21:23:51 UTC  
> Url: https://github.com/boostorg/beast/pull/957#discussion_r159125325  

This is so it shows up in the generated documentation


---
