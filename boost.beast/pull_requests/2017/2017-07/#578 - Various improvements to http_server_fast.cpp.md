# #578 Various improvements to http_server_fast.cpp: [Closed]

> Username: chriskohlhoff  
> Created at: 2017-07-03 13:03:57 UTC  
> Updated at: 2017-07-05 01:25:50 UTC  
> Closed at: 2017-07-05 01:25:50 UTC  
> Url: https://github.com/boostorg/beast/pull/578  

- Receive request in a single read (addresses #574)  
- Use fields_alloc for response  
- Fix command line usage information  
- Add command line option to spin the io_service

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-03 13:10:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/578#pullrequestreview-47660441  

📁 example/http-server-fast/http_server_fast.cpp

```diff
  58 |     // The socket for the currently connected client.
  61 |-     tcp::socket socket_;
  59 |+     tcp::socket socket_{acceptor_.get_io_service()};
```

> Username: vinniefalco  
> Created_at: 2017-07-03 13:10:36 UTC  
> Updated_at: 2017-07-03 22:21:54 UTC  
> Url: https://github.com/boostorg/beast/pull/578#discussion_r125285524  

is this C++11?

> Username: chriskohlhoff  
> Created_at: 2017-07-03 22:20:37 UTC  
> Updated_at: 2017-07-03 22:21:54 UTC  
> Url: https://github.com/boostorg/beast/pull/578#discussion_r125363389  

yep


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-03 13:11:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/578#pullrequestreview-47660559  

📁 example/http-server-fast/http_server_fast.cpp

```diff
  72 |-     boost::asio::basic_waitable_timer<std::chrono::steady_clock> request_deadline_;
  71 |+     boost::asio::basic_waitable_timer<std::chrono::steady_clock> request_deadline_{
  72 |+         acceptor_.get_io_service(), std::chrono::steady_clock::time_point::max()};
```

> Username: vinniefalco  
> Created_at: 2017-07-03 13:11:10 UTC  
> Updated_at: 2017-07-03 22:21:54 UTC  
> Url: https://github.com/boostorg/beast/pull/578#discussion_r125285682  

I think we need to parenthesis  
```  
(std::chrono::steady_clock::time_point::max)()  
```

> Username: chriskohlhoff  
> Created_at: 2017-07-03 22:20:57 UTC  
> Updated_at: 2017-07-03 22:21:54 UTC  
> Url: https://github.com/boostorg/beast/pull/578#discussion_r125363408  

aaaaah Windows


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2017-07-03 13:43:18 UTC  
> Updated_at: 2017-07-03 22:49:49 UTC  
> Url: https://github.com/boostorg/beast/pull/578#issuecomment-312649159  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=h1) Report  
> Merging [#578](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/78a065ba39836d91d7e70d93de7f9140f518083b?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/578/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #578      +/-   ##  
===========================================  
+ Coverage    93.77%   93.79%   +0.01%       
===========================================  
  Files           94       94                
  Lines         7393     7368      -25       
===========================================  
- Hits          6933     6911      -22       
+ Misses         460      457       -3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `85.18% <0%> (-7.13%)` | :arrow_down: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `90.62% <0%> (-6.05%)` | :arrow_down: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `82.6% <0%> (-4.9%)` | :arrow_down: |  
| [include/beast/websocket/impl/close.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `97.18% <0%> (-0.22%)` | :arrow_down: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.73% <0%> (-0.03%)` | :arrow_down: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `91.66% <0%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `86.84% <0%> (+0.19%)` | :arrow_up: |  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `87.5% <0%> (+0.43%)` | :arrow_up: |  
| [include/beast/websocket/impl/ping.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `96.29% <0%> (+2.96%)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <0%> (+14.28%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=footer). Last update [78a065b...4640763](https://codecov.io/gh/vinniefalco/Beast/pull/578?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: chriskohlhoff  
> Created_at: 2017-07-03 22:27:18 UTC  
> Url: https://github.com/boostorg/beast/pull/578#issuecomment-312743617  

> On 4 Jul 2017, at 12:39 am, Vinnie Falco <notifications@github.com> wrote:  
>   
> I'm a little confused about the handling of the timer. The docs say that expires_from_now cancels pending waits. But in http_server_fast.cpp we call expires_from_now followed by read_header and never establish a wait. Is this correct?  
  
The check_deadline function "loop" is responsible for doing the waiting. It will wake up and then go to sleep again on the new timeout.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-07-03 22:29:13 UTC  
> Url: https://github.com/boostorg/beast/pull/578#issuecomment-312743821  

@chriskohlhoff Very nice way to abuse.. I mean, use :) the timer! I will be stealing that.

---

## Comment 6

> Username: chriskohlhoff  
> Created_at: 2017-07-03 22:31:58 UTC  
> Updated_at: 2017-07-03 22:42:31 UTC  
> Url: https://github.com/boostorg/beast/pull/578#issuecomment-312744086  

On Tue, Jul 4, 2017, at 08:29 AM, Vinnie Falco wrote:  
> @chriskohlhoff Very nice way to abuse.. I mean, use :) the timer! I will  
> be stealing that.  
  
The Boost.Asio example program example/cpp03/timeouts/server.cpp  
illustrates this approach in more detail.  
  
EDIT: Also this: http://blog.think-async.com/2010/04/timeouts-by-analogy.html

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-07-04 23:26:43 UTC  
> Url: https://github.com/boostorg/beast/pull/578#issuecomment-312966339  

Always nice to find out I'm behind the curve on a 7 year old blog post

---
