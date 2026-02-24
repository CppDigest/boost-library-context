# #2086 - Question about websocket::stream::async_read_some behavior [Closed]

> Username: hoditohod  
> Created at: 2020-09-11 20:12:57 UTC  
> Updated at: 2024-06-06 05:34:21 UTC  
> Closed at: 2021-05-29 17:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2086  

Beast version: Boost 1.68  
OS: Ubuntu 16.04 / Windows Server 2016  
  
I have Beast WebSocket server application that receives/transmits fairly large websocket messages in stream mode. When  
receiving it uses websocket::stream::async_read_some with a flat_buffer and 256k size limit. The received buffers are  
immediately (in the completion handler) forwarded on channel which has large per operation overhead so large buffers are  
preferred compared to small ones.  
  
To test the server I use a Beast based synchronous websocket client with websocket::stream::write_some method and auto_fragment  
turned off. As I understand this creates 1 frame per call (correct me if I'm wrong!). When I transfer an 50MB message  
with various frame sizes I observe the following:  
- 8k frame size: async_read_some returns with ~7k, ~1k sizes periodically (7,1,7,1...) where the sum of ~7k+~1k = 8192  
- 128k frame size: same behavior, but the buffers are ~127k, ~1k  
  
Questions:  
- **why do I receive the frame in 2 calls (even small ones)? How can I avoid this?**  
- **I don't have control over the fragmentation done by the clients in production, how can I instruct Beast to hand me over  
all data that's available without blocking?**  
  
I assume at frames sizes as low as 4k, the data available at TCP level is enough for more than 1 frame. Also, data needs to be   
forwarded in a timely manner, so I can't call async_read_some repeatedly until my desired buffer size  
reached, as this would eventually block.  
  
Any help is much appreciated!    
Great library by the way!!!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-11 20:58:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-691308849  

Is there any way we can set up a test harness to see what's going on?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-11 21:54:53 UTC  
> Updated at: 2020-09-11 21:55:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-691328790  

>  I can't call async_read_some repeatedly  
  
Can you simply call `websocket::stream::async_read` to read the entire message (no matter how large it is) and call it a day?

---

## Comment 3

> Username: hoditohod  
> Created at: 2020-09-12 13:42:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-691490318  

The basic idea is to forward websocket frames as they arrive, maintaining an interactive rate. If there's a 5 second gap between frame N and N+1, then N must be forwarded immediately in the completion handler, and not wait for N+1. But if both frames are really small (say few kb's), and both are immediately available (async_read_some for N+1 would complete in a few millisecs) then - because of the per message overhead in the receiver - I would like forward them as one message.  
  
From code point of view: in the async_read_some completion handler I have the following dilemma:   
- should I forward the received data immediately (maintain interactive pace, but increase overhead due to potentially small messages)  
- or should I call async_read_some again (increase message size, but potentially block and loose interactivity)   
  
TCP non-blocking mode would solve that, but as far as I know that's not compatible with websocket::stream.  
If it's not generally possible to solve this with Beast, then receiving a singe frame in a single call would still halve the overhead that I experience now (1st question in original post).  
  
I'll craft some example code to demonstrate my observations.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-12 14:12:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-691495277  

> The basic idea is to forward websocket frames as they arrive  
  
Do you mean forward them to the completion handler, or do you mean forward as in - you are building a websocket router which sends the frames to another remote peer?

---

## Comment 5

> Username: hoditohod  
> Created at: 2020-09-13 20:27:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-691720906  

Send frames to a remote peer.  
  
I created a small example here: https://gist.github.com/hoditohod/af71163eb6822f8964188b81482a5d45  
And attached some execution results (showing frames delivered in multiple calls). The ZeroMQ parts are only there for illustration, only Boost is needed for the build.  
  
I hope it's more clear what I try to achieve. ~An image~ code tells a thousand words. :)

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-09-14 05:45:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-691825227  

Thank you. Will investigate.

---

## Comment 7

> Username: hoditohod  
> Created at: 2020-10-01 13:58:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-702155263  

Hi, is there any progress on this? Why it takes multiple calls to async_read_some to return a singe frame?

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-10-01 15:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-702207205  

> Why it takes multiple calls to async_read_some to return a singe frame?  
  
Because this is how `async_read_some` works(). If you want one frame at a time, use `async_read`()

---

## Comment 9

> Username: hoditohod  
> Created at: 2020-10-05 07:30:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-703454789  

😄 `async_read` returns a whole message, I'm talking about frames. Please have a look at the linked gist with the example and execution results. `async_read_some` fragments even small 8kbyte WS frames into 2-3 calls.

---

## Comment 10

> Username: hoditohod  
> Created at: 2020-10-20 12:06:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-712802389  

I went through the documentation again, and peeked into the code. For some reason I had the impression that `websocket::stream::async_read_some` returns one websocket frame per call. This is the case for `async_write_some`: it transmits a whole frame, but read works differently. As I understood the code the first part of the frame is always read into `websocket::stream::rd_buf_` which is static 1536 byte, that's why I see 1500ish values in the first completion handler, and the later transferredBytes of the frame basically depend on ASIO tcp::socket::async_read_some, which returns as many bytes as it sees fit. :)  
  
I got just one more question before closing this issue: does Beast.Websocket provide any means to observe frame boundaries? The control_callback allows me to observe everything _except_ actual data frames.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2020-10-20 15:02:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-712918352  

> does Beast.Websocket provide any means to observe frame boundaries?   
  
No. And a program should not rely on frame boundaries, as intermediates such as proxies (and Beast itself) are free to reframe messages any way they want.

---

## Comment 12

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-751240922  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: stale[bot]  
> Created at: 2021-05-29 17:10:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2086#issuecomment-850866698  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
