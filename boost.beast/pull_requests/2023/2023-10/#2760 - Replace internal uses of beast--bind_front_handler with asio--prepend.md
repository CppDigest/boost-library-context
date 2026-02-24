# #2760 Replace internal uses of beast::bind_front_handler with asio::prepend [Merged]

> Username: ashtum  
> Created at: 2023-10-27 05:05:36 UTC  
> Updated at: 2023-12-30 05:17:33 UTC  
> Merged at: 2023-12-30 05:17:33 UTC  
> Closed at: 2023-12-30 05:17:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2760  

We cannot deprecate `beast::bind_front_handler` in favor of `asio::prepend` or `asio::append` because it has a [special overload](https://github.com/boostorg/beast/blob/667e9ea251e862fa7b62a3ee94054faef201702e/include/boost/beast/core/detail/bind_handler.hpp#L240) for cases when the handler is a member function pointer which enables code like this to work:  
  
```C++  
ws_.async_accept(  
    req,  
    beast::bind_front_handler(  
        &websocket_session::on_accept,  
        shared_from_this()));  
```  
While this pattern is only employed in examples and tests, all instances within the library itself can be replaces with `asio::prepend`.

---

## Review 1 [Commented]

> Username: ashtum  
> Created_at: 2023-10-27 06:35:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2760#pullrequestreview-1701083436  

📁 test/beast/websocket/close.cpp

```diff
 777 |             BEAST_EXPECT(count == 3);
 778 |-             BEAST_EXPECT(ic == 6);
 778 |+             BEAST_EXPECT(ic == 3);
```

> Username: ashtum  
> Created_at: 2023-10-27 06:35:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2760#discussion_r1374129140  

@klemens-morgenstern Do you have any idea about the reason?

> Username: ashtum  
> Created_at: 2023-12-29 09:45:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2760#discussion_r1438120377  

Resolved in: https://github.com/boostorg/beast/pull/2782


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-10-27 07:24:53 UTC  
> Updated_at: 2023-12-19 10:30:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2760#issuecomment-1782428838  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2760](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c15bd4f) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/192b7d5a43a7a95dd29df8b08cff635fb14ad9a6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (192b7d5) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2760/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2760   +/-   ##  
========================================  
  Coverage    93.01%   93.01%             
========================================  
  Files          178      178             
  Lines        13688    13678   -10       
========================================  
- Hits         12732    12723    -9       
+ Misses         956      955    -1       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/beast/\_experimental/test/impl/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaHBw) | `99.31% <100.00%> (+0.63%)` | :arrow_up: |  
| [...ude/boost/beast/\_experimental/test/impl/stream.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaXBw) | `97.54% <ø> (ø)` | |  
| [include/boost/beast/\_experimental/test/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3Qvc3RyZWFtLmhwcA==) | `75.00% <ø> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/beast/core/impl/buffered\_read\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaHBw) | `91.37% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.45% <100.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/beast/http/impl/write.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `90.35% <100.00%> (-0.09%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `95.16% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `95.87% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/teardown.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5ocHA=) | `78.26% <100.00%> (-0.32%)` | :arrow_down: |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [192b7d5...c15bd4f](https://app.codecov.io/gh/boostorg/beast/pull/2760?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: fpelliccioni  
> Created_at: 2023-10-27 08:00:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2760#pullrequestreview-1701207300  

Most of the changes look good and make sense, what concerns me is the change in the test. Although if I think about it better maybe the test values now even make more sense. Maybe it would be better to compare by greater or equal instead of equal. Let's see what @klemens-morgenstern  thinks.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-10-27 08:14:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2760#issuecomment-1782493142  

Not this release guys. I think deprecating the public `bind_front` and replacing the usage within beast is a good idea. But you'll need to check for things like internal posts in the handler and make sure we're not breaking any thing by introducing recursion. The test change tells me we're missing something, especially since you don't know what causes it.

---
