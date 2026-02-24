# #662 fixed build error (src/test/benchmarks/utf8_checker.cpp:99:21: error: 'clock_type' is a private member of 'beast::utf8_checker_test::timer') [Closed]

> Username: adrianimboden  
> Created at: 2017-07-17 11:30:18 UTC  
> Updated at: 2017-07-18 23:57:58 UTC  
> Closed at: 2017-07-18 23:57:58 UTC  
> Url: https://github.com/boostorg/beast/pull/662  

When I tried to build the project (clang 4.0.1), this compilation error occurred.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-07-17 11:39:01 UTC  
> Url: https://github.com/boostorg/beast/pull/662#issuecomment-315731937  

That's bizarre... which branch is this? Why doesn't the error show up on CI?

---

## Comment 2

> Username: adrianimboden  
> Created_at: 2017-07-17 11:54:03 UTC  
> Url: https://github.com/boostorg/beast/pull/662#issuecomment-315734707  

I cloned the latest develop branch. The only reason I could think of, is that the CI does not build the performance tests.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2017-07-17 11:57:34 UTC  
> Updated_at: 2017-07-17 13:04:44 UTC  
> Url: https://github.com/boostorg/beast/pull/662#issuecomment-315735432  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=h1) Report  
> Merging [#662](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/37199d4d4ae5eff11d59c60473871e5b6b7fa3e8?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/662/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #662      +/-   ##  
===========================================  
+ Coverage    93.99%   93.99%   +<.01%       
===========================================  
  Files          106      106                
  Lines         8073     8074       +1       
===========================================  
+ Hits          7588     7589       +1       
  Misses         485      485  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `91.3% <100%> (+0.39%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=footer). Last update [37199d4...ecbcc0d](https://codecov.io/gh/vinniefalco/Beast/pull/662?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-17 12:38:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/662#pullrequestreview-50305458  

📁 include/beast/websocket/stream.hpp

```diff
 633 |+     closed() const
 634 |+     {
 635 |+         return wr_close_ || rd_close_;
```

> Username: vinniefalco  
> Created_at: 2017-07-17 12:38:31 UTC  
> Updated_at: 2017-07-17 12:39:45 UTC  
> Url: https://github.com/boostorg/beast/pull/662#discussion_r127696135  

This will be unnecessary soon. If `rd_close_` is ever true, it means that `websocket::error::closed` has already been delivered to the caller. If `wr_close_` is true, it means the caller invoked `close` or `async_close`. And soon, those functions will also drain the connection and deliver `websocket::error::closed` to the caller to indicate a proper closure. So this function won't make much sense. Also, a newly constructed stream will cause this function to return false, which also doesn't make sense.  
  
I appreciate your efforts though! I should probably update the issue

> Username: adrianimboden  
> Created_at: 2017-07-17 12:48:31 UTC  
> Url: https://github.com/boostorg/beast/pull/662#discussion_r127698110  

Ah perfect. I got caught by this, because the websocket::stream::close method currently asserts when the socket is already closed.

> Username: vinniefalco  
> Created_at: 2017-07-17 13:28:52 UTC  
> Url: https://github.com/boostorg/beast/pull/662#discussion_r127707214  

You shouldn't be calling `async_close` twice.  If there is a scenario you can figure out that causes `async_close` to assert even when you only call it once, then that's a bug and needs to be fixed. The only case I can think of is if the implementation receives a close frame, sends one in response, and then before it delivers `websocket::error::close`, the application calls `async_close`. I can't prove that its possible though.  
  
Now that I look over the async code it appears it is missing the assert present in the synchronous code, i will open an issue.  
  
And I will refactor the close code to be more robust.

> Username: vinniefalco  
> Created_at: 2017-07-17 13:31:35 UTC  
> Url: https://github.com/boostorg/beast/pull/662#discussion_r127707843  

Now that I look at it more closely, there's a smell coming out of this close code...you are on to something!

> Username: vinniefalco  
> Created_at: 2017-07-17 13:40:24 UTC  
> Updated_at: 2017-07-17 13:40:25 UTC  
> Url: https://github.com/boostorg/beast/pull/662#discussion_r127710058  

Are you using the synchronous version of `close`?

> Username: adrianimboden  
> Created_at: 2017-07-17 16:11:05 UTC  
> Url: https://github.com/boostorg/beast/pull/662#discussion_r127751444  

Yes, I use the synchronous version of close. But only in the shutdown case, everything else is asynchronous.  
  
the function stream<NextLayer>::close(close_reason const& cr, error_code& ec) asserts just before checking it to set the error code  
  
I did some debugging yesterday, and I'm pretty sure, that the close function wasn't called twice. It was called just after after upgrading the connection, but before transmitting or receiving any data yet, if I remember correctly.


---
