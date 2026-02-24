# #1070 - documentation clarification: allowable concurrent async operations [Closed]

> Username: vchang-akamai  
> Created at: 2018-03-17 04:05:42 UTC  
> Updated at: 2023-10-24 08:12:58 UTC  
> Closed at: 2019-02-16 13:57:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1070  

This page says we can safely do concurrent read, write, ping, close:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/notes.html  
  
 "However, this code is well-formed:  
  
    ws.async_read(b, [](error_code, std::size_t){});  
    ws.async_write(b.data(), [](error_code, std::size_t){});  
    ws.async_ping({}, [](error_code){});  
    ws.async_close({}, [](error_code){});  
  
The implementation uses composed asynchronous operations; although some individiual operations can perform both reads and writes, this behavior is coordinated internally to make sure the underlying stream is operated in a safe fashion. "  
  
Ok, sounds good, but over here:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html  
  
"This operation is implemented in terms of one or more calls to the next layer's async_write_some functions, and is known as a composed operation. The program must ensure that the stream performs no other write operations (such as websocket::stream::async_ping, websocket::stream::async_write, websocket::stream::async_write_some, or websocket::stream::async_close) until this operation completes. "  
  
Seems like the async_close() docs are contradicting the Notes doc? Or am I just misreading it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-17 15:39:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-373929487  

The reference doc for `async_close` is incorrect. The Notes is accurate, thanks for finding this!

---

## Comment 2

> Username: vchang-akamai  
> Created at: 2018-03-19 19:06:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-374331052  

I noticed other similar wording in the docs for async_ping(), async_pong(), async_write(). You may want to review those as well.

---

## Comment 3

> Username: JunielKatarn  
> Created at: 2019-02-15 10:31:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-463989545  

Does this mean there is no guaranteed dispatching sequence between, say, async-write and async-close, regardless of the order in which they were called?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-15 12:29:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-464032020  

> guaranteed dispatching sequence  
  
I'm not sure what this means, but the websocket stream lets you call `async_write` and then `async_close` before the write completes, and behind the scenes it juggles these two operations in a way that preserves the invariants of the next layer.  
  
After you call `async_close` though, you can't call anything else - because the WebSocket RFC states that you should not send any more data, and because `async_close` reads everything for you until it gets that matching close frame.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-15 12:30:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-464032444  

I have rewritten all of the docs for each member function of the websocket stream, have a look  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read_some/overload1.html  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_ping.html  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html

---

## Comment 6

> Username: JunielKatarn  
> Created at: 2019-02-15 22:09:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-464221125  

> and behind the scenes it juggles these two operations in a way that preserves the invariants of the next layer  
  
What I mean is whether the `async_close` call would interrupt an `async_write` call being performed (actual writing happening).  
From what you wrote (quoted), I infer it's up to 'next layer'. Does this mean the answer I seek is actually in the TCP standard?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-02-15 22:25:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-464226745  

> What I mean is whether the `async_close` call would interrupt an `async_write` call being performed (actual writing happening).  
  
No, the close can't "interrupt" the write. The way that it works, if a write is outstanding when a close is performed, then the close operation is "saved" in the stream. When the write operation receives an intermediate completion, and it is in a state where it can give control back, it suspends itself and "resumes" the close operation so that the close can be written out and the matching close frame received. When the close is complete, it will "resume" the paused write operation, which then detects that the stream is closed and delivers `net::error::operation_aborted` to the completion handler.  
  
Have a look for yourself, here we suspend the close operation if a write is active:  
https://github.com/boostorg/beast/blob/d43d9421a40c0251614bc45ea6dcf921a3dbaf37/include/boost/beast/websocket/impl/close.hpp#L89  
  
This is where we let the control frame through during a write:  
https://github.com/boostorg/beast/blob/d43d9421a40c0251614bc45ea6dcf921a3dbaf37/include/boost/beast/websocket/impl/write.hpp#L240  
  
> Does this mean the answer I seek is actually in the TCP standard?  
  
No, all of these behaviors are built into the beast websocket stream. At no time is there more than one read, or more than one write, taking place simultaneously on the underlying socket.

---

## Comment 8

> Username: JunielKatarn  
> Created at: 2019-02-15 22:27:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-464227399  

Got it. Thanks!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-02-16 13:57:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-464349215  

The websocket docs have been completely rewritten for asynchronous behaviors so I think this is well-addressed. If you find anything missing or unclear please open a new issue!

---

## Comment 10

> Username: ecorm  
> Created at: 2023-10-23 18:54:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1775827838  

@vinniefalco What happens when `async_close` is called after an `async_write_some` that does not send the remainder of a complete message? Does the `async_close` immediately take over, or is it "saved" until the remainder of the message is written via `async_write_some`?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2023-10-23 18:55:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1775830563  

@fpelliccioni @ashtum you two need to answer this by looking at the implementation. Show me your findings and I will verify it.

---

## Comment 12

> Username: ecorm  
> Created at: 2023-10-23 18:58:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1775837820  

> @fpelliccioni @ashtum you two need to answer this by looking at the implementation. Show me your findings and I will verify it.  
  
I can try taking a look, but I won't pretend to have a deep understanding Beast's implementation (not meant as criticism, just lack of time to deep-dive into the library). :-)

---

## Comment 13

> Username: vinniefalco  
> Created at: 2023-10-23 19:20:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1775874903  

I rather these fellas take a look as they are tasked with maintenance of the lib. You can look too but please reserve your findings until they are done.

---

## Comment 14

> Username: ecorm  
> Created at: 2023-10-23 19:23:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1775878566  

> I rather these fellas take a look as they are tasked with maintenance of the lib. You can look too but please reserve your findings until they are done.  
  
Understood. What I can do more competently is write up a minimal example.

---

## Comment 15

> Username: ecorm  
> Created at: 2023-10-23 20:26:38 UTC  
> Updated at: 2023-10-23 20:36:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1775966251  

I have modified the async websocket client and server examples here: https://gist.github.com/ecorm/277d056550be339dcecf41b609dc8917  
  
The client has been modified to send half of the text string via `async_write_some`, and then does `async_close`.  
  
The server has been modified to print the close code and close reason string.  
  
Wireshark shows a `Websocket Text` frame followed by a `Websocket Connection Close` frame. The payloads in those frames are as expected.  
  
![Screenshot from 2023-10-23 17-19-31](https://github.com/boostorg/beast/assets/9258548/19e62f79-7802-4895-9c23-0b870fcf0087)  
  
I will reserve final conclusions to the author and maintainers, but it does seem to behave properly from a user's point of view.  
  
I hope I didn't step on anyone's toes by doing this little investigation, but I need to make an educated guess ASAP on Beast's behavior for this use case, so that I can move on with my code. If it turns out my findings don't match the conclusion of the author/maintainers, I'll go back and fix my code accordingly.  
  
I will not bother deep-diving into the code, as it's as cryptic as any other standard/Boost C++ library, and will let the maintainers do the digging.

---

## Comment 16

> Username: fpelliccioni  
> Created at: 2023-10-23 21:03:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776018129  

@vinniefalco @ecorm   
  
I've quickly glanced over the implementation of `async_write_some`, `async_close`, and their internal workings. From my brief observation:  
  
The `async_close` operation tries to acquire a write lock (`wr_block`). If it can't get the lock due to an ongoing write operation like `async_write_some`, it will wait until the write operation completes before proceeding.  
  
Thus, if `async_close` is called right after `async_write_some`, and the latter hasn't finished, `async_close` will wait for its completion.  
  
I'll verify this in more detail tomorrow to ensure my conclusion is accurate.

---

## Comment 17

> Username: ecorm  
> Created at: 2023-10-23 21:08:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776024479  

> If it can't get the lock due to an ongoing write operation like async_write_some, it will wait **until the write operation completes** before proceeding.  
  
By "write operation completes", do you mean writing an entire message, or do you mean the `async_write_some` operation?

---

## Comment 18

> Username: fpelliccioni  
> Created at: 2023-10-23 21:17:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776036640  

> > If it can't get the lock due to an ongoing write operation like async_write_some, it will wait **until the write operation completes** before proceeding.  
>   
> By "write operation completes", do you mean writing an entire message, or do you mean the `async_write_some` operation?  
  
When `async_close` is called right after an `async_write_some` that does not send the remainder of a complete message, the close operation seems to take over right after the partial message has been sent, without waiting for the entire message to be sent. The behavior observed in Wireshark with a WebSocket Text frame followed by a WebSocket Close frame further supports this conclusion.

---

## Comment 19

> Username: ecorm  
> Created at: 2023-10-23 21:29:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776051594  

@fpelliccioni , thank you for the clarification.  
  
I have [modified the client example](https://gist.github.com/ecorm/889c150d16b322734cfcca9015f47c93) so that the `async_close` is called immediately after `async_write_some` (instead of inside the `async_write_some` handler). The behavior remains the same. A subsequent `async_write_some` fails with "Operation canceled", as expected.

---

## Comment 20

> Username: ashtum  
> Created at: 2023-10-24 05:40:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776567783  

I believe the confusion arises from the fact that async_write_some in Asio can complete before the entire buffer is completely written, so it should be used with caution. However, [websocket::stream::async_write_some](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html) is not related to this behavior and always writes a complete and valid WebSocket frame. As a result, a close frame can be written afterwards without any issues.  
The purpose of websocket::stream::async_write_some is to enable users to send a message part by part and manually mark the end of the message.

---

## Comment 21

> Username: ecorm  
> Created at: 2023-10-24 06:31:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776608716  

> I believe the confusion arises from the fact that async_write_some in Asio can complete before the entire buffer is completely written, so it should be used with caution.  
  
Yes, that was indeed confusing to me.  
  
> However, [websocket::stream::async_write_some](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html) is not related to this behavior and always writes a complete and valid WebSocket frame.  
  
It would be helpful if Beast's documentation mentionned this.  
  
> The purpose of websocket::stream::async_write_some is to enable users to send a message part by part and manually mark the end of the message.  
  
I need `async_write_some` to implement timeout logic similar to Apache's `MinRate` in [`mod_reqtimeout`](https://httpd.apache.org/docs/2.4/mod/mod_reqtimeout.html), but for the server write direction (and for Websocket instead of HTTP). When a partial write completes, the timeout duration is increased according to the `MinRate`. If it weren't for that fancy timeout mechanism, I would just use `async_write`.

---

## Comment 22

> Username: ashtum  
> Created at: 2023-10-24 07:25:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776665770  

> I need async_write_some to implement timeout logic similar to Apache's MinRate in [mod_reqtimeout](https://httpd.apache.org/docs/2.4/mod/mod_reqtimeout.html), but for the server write direction (and for Websocket instead of HTTP). When a partial write completes, the timeout duration is increased according to the MinRate. If it weren't for that fancy timeout mechanism, I would just use async_write.  
  
Are you using stream::async_write_some because messages are heavy, and you want to have finer control over the timeout?

---

## Comment 23

> Username: ecorm  
> Created at: 2023-10-24 08:12:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1070#issuecomment-1776732562  

> Are you using stream::async_write_some because messages are heavy, and you want to have finer control over the timeout?  
  
Yes, it's to dynamically extend the timeout duration for larger messages. It's like in those car racing video games where you're given extra time to complete the course when you pass checkpoints.  
  
I know that a timeout proportional to the message size cold be computed a priori, but the way Apache does it with MinRate cuts off [Slow DoS](https://en.wikipedia.org/wiki/Slow_DoS_attack) attacks more eagerly.
