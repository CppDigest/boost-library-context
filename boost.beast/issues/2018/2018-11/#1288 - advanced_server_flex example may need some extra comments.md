# #1288 - advanced_server_flex example may need some extra comments [Closed]

> Username: reddwarf69  
> Created at: 2018-11-04 00:56:06 UTC  
> Updated at: 2019-03-06 22:29:09 UTC  
> Closed at: 2019-03-06 22:29:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1288  

advanced_server_flex example may need some extra comments... or maybe it's just me being more dumb than average ;-)  
  
I will admit I'm weak and until now I have been simply avoiding explicit strands/multiple threads in my code out of fear of messing up. But I was looking at the advanced_server_flex example and have struggled to understand its use of strands and came with these questions:  
  
* Why does ssl_websocket_session need its own strand (https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L550)? Couldn't it use websocket_session's one (https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L246)?  
  
* ssl_websocket_session's strand is only used to call on_shutdown (https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L603), which doesn't seem to do anything requiring a strand protection.  
  
* Isn't the whole ssl_websocket_session's strand use even more pointless? If async_shutdown() in https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L593 has been called more than once, no mater if in parallel or not, haven't you already failed? You can only shut it down once.  
  
* Even with all these strands, isn't is still possible for "ssl::stream::async_shutdown" to be executing/pending at the same time than websocket::stream::async_read() or websocket::stream::async_write()? Wouldn't that be undefined?  
  
Maybe it would make sense to add comments to the example explaining all this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-04 01:29:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-435634296  

> Why does ssl_websocket_session need its own strand   
  
Actually, that looks like a bug. It should use the strand in the parent class, `websocket_session`. Good find!  
  
> ...isn't is still possible for "ssl::stream::async_shutdown" to be executing/pending at the same time than websocket::stream::async_read() or websocket::stream::async_write()? Wouldn't that be undefined?  
  
Yes it is possible, and no it is not undefined. The implementation takes care of all the multiplexing and coordination of asynchronous reads and writes for you. See:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety  
  
> Maybe it would make sense to add comments to the example explaining all this?  
  
Yep, at some point I will improve the examples with respect to comments and documentation. But remember, Beast assumes that you are already a whiz at Asio!

---

## Comment 2

> Username: reddwarf69  
> Created at: 2018-11-04 10:26:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-435657943  

> Yes it is possible, and no it is not undefined. The implementation takes care of all the multiplexing and coordination of asynchronous reads and writes for you. See:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety  
  
Reading that documentation I am happy with calling `websocket::stream::async_close()` at the same time than websocket::stream::async_read() and websocket::stream::async_write(). But in this case `websocket::stream::async_close()` is not being called at all, it jumps directly to `ssl_stream::async_shutdown()`. Is this also safe? I am supposing it's unsafe to call write operations of different layers at the same time. Also, isn't it a bit inconsistent for `plain_websocket_session` to be closing the websocket session cleanly with `websocket::stream::async_close()`, but then `ssl_websocket_session` skipping that step?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-11-04 11:14:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-435660804  

Hmmm it is inconsistent, I will have to take a look at that.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-11-04 15:00:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-435676685  

> `async_close` is not being called at all  
  
Actually, this behavior is correct. `websocket::stream` automatically handles incoming close frames during reads. The read will complete with the error `end_of_stream`. This indicates that the close handshake process has completed successfully:  
  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/control_frames.html#beast.using_websocket.control_frames.close_frames  
  
Furthermore, the implementation automatically calls `ssl_stream::async_shutdown` for you. This is required by the WebSocket protocol. In the example, `websocket_session::on_read` checks for `websocket::error::closed` and in that case simply returns. This ultimately results in destroying the socket.  
  
So yes there is an apparent inconsistency, but it is by design - the WebSocket implementation automatically closes the socket for you (or in the case of a TLS connection performs the closing handshake). This process is called "teardown" and documented here:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/teardown.html

---

## Comment 5

> Username: reddwarf69  
> Created at: 2018-11-04 22:58:26 UTC  
> Updated at: 2018-11-04 23:00:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-435716540  

If I got it right...  
  
* In the plain TCP case:  
a) It may receive a close frame in any moment. It will send a close frame back and through async_teardown the TCP socket will be closed. But if the client simply "disappears" async_teardown could "block" in https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/teardown.ipp#L134, couldn't it? After 15 seconds the timeout will kick, making it send a second close frame (https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L519), is this legal? But we may still be blocked in the async_wait. After another 15 seconds it will reach https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L512 and there will be no more timeouts. And the sessions will stay there potentially forever.  
  
b) Something very similar may happen if it never receives a close frame. The client dissapears, there is a timeout, a websocket close frame is sent... and the session can potentially stay there forever "blocked" in `do_read` waiting to receive a websocket close frame reply.  
  
  
* In the SSL case  
a) When a close frame is received the async_teardown for tcp::socket will not be used at all. There will be an attempt to close the SSL connection cleanly. And in practice the TCP connection will be closed good enough. But there may be some TCP packets with a RST flag involved. It behaves a bit different than in the plain TCP case.  
Also the ssl::stream::async_shutdown called via async_teardown could "block" forever if the client doesn't reply. Similar situation to the plain TCP case.  
At some point there will be a timeout, this time `ssl::stream::async_shutdown` will be called instead of `websocket::stream::async_close`. What will happen with the `websocket::stream::async_read` operation?  
  - It sends a SSL close_notify AGAIN?... if so, will `websocket::stream::async_read` fail at this point? If it doesn't it could stay in this state forever  
  - It receives a SSL close_notify reply. I'm guessing `websocket::stream::async_read` will fail at this point. But it will report a SSL error when in fact the problem has been a Websockets-level timeout.  
Also, I guess https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L429 should be `return fail(ec, "read");` instead of `fail(ec, "read");`, no?  
   
b) If what happens is that it reaches https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L613 without first received a close frame the situation would be similar. But without the possible duplicated SSL close_notify message.  
  
  
Unless I'm missing something either keep_alive is used or at some point (after the timeout has expired enough times) there is a need to explicitly call `basic_stream_socket::cancel`, `basic_stream_socket::close` or `basic_stream_socket::shutdown(shutdown_receive)`.

---

## Comment 6

> Username: reddwarf69  
> Created at: 2018-11-05 09:35:29 UTC  
> Updated at: 2018-11-05 09:37:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-435810800  

Notice also that `advanced_server_flex` with `plain_websocket_session` behaves different to `advanced_server`. On timeout advanced_server_flex tries to close the connection cleanly with `websocket::stream::async_close` (https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L519). On that same timeout `advanced_server` goes directly to close the TCP connection (https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L323).  
  
In general I would say there is a problem in that "what's the correct thing to do?" depends on the "state". When reaching plain_websocket_session::do_timeout() has it already sent a close frame (via async_read -> async_teardown) or not? Has it already sent a TCP FIN (`shutdown(:shutdown_send)`) packet or not? Depending on the answer the right thing to do when reaching plain_websocket_session::do_timeout() could be:  
- websocket::stream::async_close  
- Try to close the TCP connection nicely like in async_teardown(role_type role, boost::asio::ip::tcp::socket& socket, TeardownHandler&& handler)  
- Forcefully close the TCP connection like in https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L323  
  
But since all this happens automagically inside `websocket::stream::async_read` we don't have access to the current "state", so we can't know what the correct thing to do is.  
If you try to clean nicely you risk trying to send a duplicated close frame or `shutdown(:shutdown_send)` the TCP connection twice.  
This is not a big deal with the plain TCP connection. You can always go and close not nicely. Not ideal, but OK.  
  
But since to securely close the SSL connection you need to call `ssl_stream::async_shutdown` this seems like a bigger issue here.

---

## Comment 7

> Username: reddwarf69  
> Created at: 2018-11-06 10:57:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-436213135  

> In the example, websocket_session::on_read checks for websocket::error::closed and in that case simply returns. This ultimately results in destroying the socket.  
  
In case of websocket::error::closed shouldn't timer_.cancel() be called so timer_.async_wait() completes with boost::asio::error::operation_aborted?  
  
@vinniefalco I see this issue was closed automatically with 04bac8d, I can't reopen it. Tell me if I should open a new issue.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-11-07 01:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-436470652  

I am not convinced the timer code is correct.

---

## Comment 9

> Username: reddwarf69  
> Created at: 2018-11-07 09:36:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-436562772  

Sorry to put all my worries with this example in a single issue. I guess I'm looking for confirmation I am not imagining things (I think the "hidden state" thing would mean the whole async_teardown logic would need to be removed from Beast and pass the reponsability of closing all the stream layers to the user) before start opening lots of potentially wrong issues.  
  
I was now looking at how a signal to stop is handled (https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L1311). The documentation says this example shows how to "Clean exit via SIGINT (CTRL+C) or SIGTERM" (kill)". But, since what it does is simply calling `boost::asio::io_context::stop()`... AFAICS all the nice cleaning of the SSL and TCP layers (and even Websocket) will not happen. The sockets will simply be destroyed.  
  
To actually exit cleanly shouldn't the example give `stop()` methods to the sessions classes and call them from the signal handler like in https://github.com/boostorg/asio/blob/develop/example/cpp11/http/server/server.cpp#L89 ? I understand this could delay the actual stopping of the program a few seconds, but it seems like the only way to actually stop _cleanly_. I don't know, maybe it was done this way on purpose to avoid making the example too complex, but in such a case maybe a comment should be added to avoid people blindly copying the style without thinking twice about it?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-11-07 11:55:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-436599802  

> maybe it was done this way on purpose to avoid making the example too complex   
  
Yes that's exactly right  
  
> in such a case maybe a comment should be added to avoid people blindly copying the style without thinking twice about it?  
  
That is something I can consider. I might also add an example of how to do the super-clean stop.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-02-23 03:41:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-466612834  

websocket has built-in configurable timeouts now,. @RedDwarf69 if you would like to take a look at the relevant examples, and let me know if they are sufficient? The master and develop branches have been updated, these changes will go into Boost 1.70. Thanks!

---

## Comment 12

> Username: reddwarf69  
> Created at: 2019-03-06 09:57:27 UTC  
> Updated at: 2019-03-06 09:59:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-470044289  

Lots of new stuff in there!  
  
The make_strand() link in https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference.html is broken, which doesn't help. But the "// The new connection gets its own strand" comment plus the explanation in https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/timeouts.html makes it clear.  
  
  
Commenting as I read it:  
  
* Why is the `acceptor_.is_open()` check here? -> https://github.com/boostorg/beast/blob/fca4b3ae10b721d201ef9d9854f0ee8d5661e601/example/advanced/server-flex/advanced_server_flex.cpp#L920  
  - Nothing calls `acceptor_.close()`, so `is_open()` will always return true, no?  
  - If I add a `stop()` method to listener, calling `acceptor_.close()`, then the check would make sense. But then shouldn't I by calling it always? i.e. shouldn't https://github.com/boostorg/beast/blob/fca4b3ae10b721d201ef9d9854f0ee8d5661e601/example/advanced/server-flex/advanced_server_flex.cpp#L953 be calling `run()` instead of `do_accept()`?  
  
  
* Should basic_stream let me specify the timer type?  
It seems to have a (steady_timer?) hardcoded?  
I am thinking mainly about testing. I may want to write a test to check my class does what I expect it to do when the timeout expires. For this I may want to have my own timer, probably a fake one that lets me control time. So in my test I can say  
  
```  
fake_timer->set_time(45 seconds in the future)  
ioc->poll()  
```  
  
And check my timeout handling code has been executed, without having to wait 30 seconds for my unit test run to finish.  
  
  
* Why does async_detect_ssl use a boost::tribool?  
I can't find a clear explanation in the async_detect_ssl function docs either (https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__async_detect_ssl.html). When does it make a difference if it returns false or indeterminate? Why would it be indeterminate?  
  
After further looking I can actually find some explanation in https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/writing_composed_operations/detect_ssl.html. Since the flat_buffer doesn't have a maximum size then async_detect_ssl() will never return indeterminate, right?  
  
Now, I don't really want to learn the details of how async_detect_ssl() works, I only want to use it. But finding out about the indeterminate status did raise a question: Could a malicious client make "async_detect_ssl" allocate gigabytes of memory if I don't give a maximum size to flat_buffer?

---

## Comment 13

> Username: reddwarf69  
> Created at: 2019-03-06 13:08:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-470100680  

From a quick look it looks like the maximum SSL "record" size is quite limited (but there are extensions to make it smaller/bigger?). So the "gigabytes of memory" issue should never happen... maybe megabytes? Still, a comment clarifying this point would be helpful for anybody not familiar with SSL.  
  
* I am unsure about whether to expect `timeout` or `operation_aborted` errors  
In https://github.com/boostorg/beast/blob/fca4b3ae10b721d201ef9d9854f0ee8d5661e601/example/advanced/server-flex/advanced_server_flex.cpp#L789 it's checking for `net::error::operation_aborted`. Why isn't it checking for timeout (https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__error.html)?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-03-06 13:25:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-470105669  

> Why is the acceptor_.is_open() check here? ->  
  
Yeah I"m not sure why, it could be removed I guess.  
  
> Should basic_stream let me specify the timer type?  
  
Nope, because condition variables don't work with user-defined timers. Although this is something we might explore changing.  
  
> Why does async_detect_ssl use a boost::tribool?  
  
Hmm, maybe it can be changed to a regular bool  
  
> Could a malicious client make "async_detect_ssl" allocate gigabytes of memory if I don't give a maximum size to flat_buffer?  
  
No. Once it gets 9 bytes, there is a guaranteed result:  
https://github.com/boostorg/beast/blob/31331bbe4d4d666bd17336790da0a2e86af4cb0e/include/boost/beast/core/detect_ssl.hpp#L156  
  
> Why isn't it checking for timeout   
  
That `if(ec == net::error::operation_aborted)` shouldn't be there at all, it was a leftover

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-03-06 13:50:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-470114001  

See #1503

---

## Comment 16

> Username: reddwarf69  
> Created at: 2019-03-06 14:41:55 UTC  
> Updated at: 2019-03-06 14:53:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-470131927  

> Hmm, maybe it can be changed to a regular bool  
  
I didn't notice it was about just 9 bytes xD I don't really mind, I just think if it stays a tribool it would need to be explained in https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__async_detect_ssl.html  
  
  
> See #1503  
  
Cool! I am starting to think I should stop worrying about any hard problem and just wait for Beast to implement the solution better than me.  
This morning I was looking at a `// XXX: I should use the handler associated allocator` comment in one of my operations, using something similar to `async_base` with an unique_ptr not looking at the allocator... then I saw Boost 1.70 will give me `stable_async_base`.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2019-03-06 15:02:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1288#issuecomment-470140082  

> Cool! I am starting to think I should stop worrying about any hard problem and just wait for Beast to implement the solution better than me.  
  
Hah well thanks. If you do run into a hard problem please open an issue and we can discuss it, if it is in scope for Beast then of course I want to support it.  
  
> then I saw Boost 1.70 will give me stable_async_base.  
  
Beast already has `handler_ptr` which does essentially the same thing, but the interface on that is not quite as good (I will deprecate it soon). You also need to be careful what you are storing in there, I wouldn't put a multi-megabyte JSON object in that. It is okay for small things that need to be stable (like a `std::string` that uses the small buffer optimization).
