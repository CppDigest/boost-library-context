# #2317 - websocket and prioritised handlers [Closed]

> Username: shuras109  
> Created at: 2021-09-23 13:33:35 UTC  
> Updated at: 2022-09-24 05:09:16 UTC  
> Closed at: 2022-09-24 05:09:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2317  

Hi there!  
  I've tried to use priority queue (get it straight from https://www.boost.org/doc/libs/1_77_0/doc/html/boost_asio/example/cpp03/invocation/prioritised_handlers.cpp example) with beast websocket and mostly it worked fine. But under stress-testing with more than 20 thousand of connections I've start to see a problem which is not present if you are running without prioritised handlers queue.  
  Each of websockets in my stress-test scenario loaded with binary frames coming in and out at a moderate speed and there is also occasional ping-pong exchanges at a slow steady pace. My problem is that somehow some of connections become stuck. Async read just never returns and even close of low level socket doesn't force this handler to finish. I've put some time in finding condition for such event and as it turns out there is always a packet received with two ws frames inside: binary and ping. I'm not sure yet if this packet created in such form originally or concatenated by network adapter offload mechanism. But in every case of such stuck ws connection I've seen was preceded by a packet with binary and ping frames inside.  
  Is there a problem inside websockeet implementation? Should it be compatible with prioritised handlers queue? Or should I stay away from it?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-09-23 13:53:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-925837871  

I don't think it should be a problem that more than one frame has been sent in the same TCP message. We treat the underlying socket as a stream, so we have no concept of TCP message boundaries in the websocket stream code (the websocket stream would work if connected to file handle or pipe, for example).  
  
1. I would like to be able to replicate your test. Would you be in a position to share the steps to create the test client and share the (complete) code that is failing?  
2. I would like a copy of the contents of the network packet in which there is both the binary frame and the ping, so that I can build a test to check the interaction of the parser and the transport layer.  
  
What you are doing sounds more complex than this example, and it might be that you have a race condition.  
  
Anecdotally, I have tested beast in a production server while handling 80,000 concurrent connections. That server has been running for three years. I suspect that if there was an inherent logic error, I would have seen it, but it is always possible that we are doing something different.

---

## Comment 2

> Username: shuras109  
> Created at: 2021-09-23 14:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-925877090  

Error only pops out for me after addition of priority queue. With plain io context there is no problem.   
I will try to create simple test basing on one of echo server tests.  
And I will provide pcap dump in a few minutes.

---

## Comment 3

> Username: shuras109  
> Created at: 2021-09-23 15:12:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-925907994  

Dump of stream with a problem. Look for a packet No 652.  
[ws.error.pcap.gz](https://github.com/boostorg/beast/files/7218803/ws.error.pcap.gz)

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-09-23 17:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-926013593  

> Dump of stream with a problem. Look for a packet No 652.  
> [ws.error.pcap.gz](https://github.com/boostorg/beast/files/7218803/ws.error.pcap.gz)  
  
I see only one frame in that tcp message though, and we sent back a `pong`.

---

## Comment 5

> Username: shuras109  
> Created at: 2021-09-27 12:31:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-927827882  

I've tried to replicate my problem in a simple test and it doesn't show up in this tests. I will go deeper in my code look for error of my own. Luckily I can replicate error with just two of concurrent sessions.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-09-28 06:03:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-928880897  

Great. This is often the case. The most common violations of asio/beast preconditions are:  
  
- not protecting a connection with a strand when there are more than one thread, resulting in data races.  
- initiating more than one async_write per stream at a time.  
- Invoking methods on a stream from one thread while there is an async operation making progress on another.  
- Not ensuring that the buffers or stream itself outlives all async operations initiated by the stream.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-1008220726  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:04:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2317#issuecomment-1256861943  

@shuras109 what's the state of this issue?
