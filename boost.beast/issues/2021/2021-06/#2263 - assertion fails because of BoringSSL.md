# #2263 - assertion fails because of BoringSSL? [Closed]

> Username: JFreyberger  
> Created at: 2021-06-16 10:34:02 UTC  
> Updated at: 2021-06-25 11:39:23 UTC  
> Closed at: 2021-06-25 07:10:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2263  

Hi,  
  
I'm using Boost 1.75 and try to run a webserver based on the beast examples. My project also includes the Google WebRTC library which comes with BoringSSL. This seems to work also for Beast but sometimes when I connect multiple browsers using SSL and doing websockets and WebRTC I run into this assertion :  
  
boost::beast::detail::stream_base::pending_guard::assign(bool & b)   
in ..\boost_1_75_0\boost\beast\core\detail\stream_base.hpp Line 111  
  
I've seen this for both read and write operations but it's hard to reproduce and happens only when there's much going on on the SSL websocket. Could this be related to the use of BoringSSL instead of OpenSSL?   
  
Thanks and best regards,  
Johannes

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-16 12:35:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862342587  

It'll probably be because you are using beast (and ASIO) incorrectly.  
  
You must ensure that only one thread is using one IO object at a time (including during outstanding async operations).  
  
You must ensure that only one async_read and/or one async_write is in progress on a stream at once.  
  
This is true of beast, asio and any library that depends on them.

---

## Comment 2

> Username: JFreyberger  
> Created at: 2021-06-16 14:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862437269  

You're probably right and I'll check my code (which is still close to the example and i.e. a async_read is only called in on_read() so the previous read should be finished? As far as I understood it's OK to have an overlapping read and write (different type) but I must prevent two overlapping calls of same type (two reads or two writes)?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-06-16 15:41:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862487159  

That is correct. The most common errors are:  
1. multiple writes  
2. more than one thread.

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-06-16 15:41:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862487616  

If it's none of those, then happy to take a look if you can help me with the SSL lib installation

---

## Comment 5

> Username: JFreyberger  
> Created at: 2021-06-16 16:01:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862504920  

Hi,  
  
thanks very much for your help. I did a first quick check in my code and added some variables and assertions to see if I have some obvious mistakes. So far my checks don't fail, but I still have the assertion in stream_base.hpp. I started to think about SSL because I found this comment in ...\boost_1_75_0\boost\beast\core\impl\basic_stream.hpp Line 288 which also appears in my calll stack. And I can only reproduce the problem if there's a lot of data transferred between the browser and my webserver through this websocket.   
  
BoringSSL is part of the WebRTC project which is a part of chromium ... that's 10 GB of code but I finally managed to compile and link with my beast based WebServer. I think I'll try if I get the problem reproduced without SSL ...  
  
Best,  
Johannes

---

## Comment 6

> Username: JFreyberger  
> Created at: 2021-06-16 16:50:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862544104  

Hmmm ... I did several tests without SSL and I pushed really hard to run into this assertion but I have never seen it so far without SSL. Now I'm not sure how to go on. I'm no SSL expert but SSL is mandatory for WebRTC once you go beyond 127.0.0.1 and our project also requires SSL. I'll probably have to try to compile WebRTC using OpenSSL ...

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-06-16 17:05:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862554641  

Not a bad idea. That will at least narrow it down.

---

## Comment 8

> Username: JFreyberger  
> Created at: 2021-06-16 18:32:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862615728  

Here some comments from Google about switching between OpenSSL and BoringSSL  
  
https://boringssl.googlesource.com/boringssl/+/HEAD/PORTING.md  
  
From your comments so far I'd guess that SSL in Beast is not yet tested to be used with BoringSSL?

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-06-16 19:19:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-862648647  

Hmm that would be an Asio question.  
@chriskohlhoff ?

---

## Comment 10

> Username: JFreyberger  
> Created at: 2021-06-18 14:28:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-864080028  

OK, so I did various builds of OpenSSL und WebRTC without BoringSSL and either I could not link because of unresolved functions or because of already defined functions and when I finally managed to get something compiled and linked WebRTC crashed inside the first SSL calls. I also found some comments that OpenSSL support in WebRTC is not something which is intended anymore and probably will fail or cause security problems. So I returned to using Boost Asio with BoringSSL and I found a #define in Boost which is OPENSSL_IS_BORINGSSL ... but this doesn't seem to do the trick.

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-06-18 15:26:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-864117391  

This is definitely an Asio question. I'll see if I can get Chris to chime in.

---

## Comment 12

> Username: JFreyberger  
> Created at: 2021-06-18 15:34:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-864121876  

I already posted this also to https://github.com/chriskohlhoff/asio/issues/852 ... thanks for your support.

---

## Comment 13

> Username: chriskohlhoff  
> Created at: 2021-06-19 07:02:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-864366915  

BoringSSL support was added through user-supplied patches, and I do not test asio with it myself. If you can create a minimal reproducer that runs with both OpenSSL and BoringSSL then we may be able to compare the behaviour of the two.

---

## Comment 14

> Username: madmongo1  
> Created at: 2021-06-25 06:56:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-868270503  

Now that the corresponding issue in the Asio repo is resolved, are we in a position to resolve this issue now?

---

## Comment 15

> Username: JFreyberger  
> Created at: 2021-06-25 07:10:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-868277617  

Due to https://stackoverflow.com/questions/18409517/boost-asio-ssl-async-read-and-async-write-from-one-thread it seems to be necessary to do everything from one strand when it comes to a SSL stream. So far this seems to have solved the problem.

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-06-25 07:18:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-868282061  

Not just SSL. Everything in asio and beast.  
Strands are there to ensure correct concurrency control with io objects

---

## Comment 17

> Username: JFreyberger  
> Created at: 2021-06-25 08:48:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-868332061  

Well, I think my misunderstanding was that just not have overlapping calls which I can also achieve from different threads with some synchronisation is not enough. There seem to be some inner asio and beast calls which still overlap and (at least to me) this seems to happen more often when it comes to SSL. So the importance of a strand vs. multiple but synchronized threads was not clear to me. Thanks again for your support.

---

## Comment 18

> Username: madmongo1  
> Created at: 2021-06-25 11:39:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2263#issuecomment-868437723  

Yes, all async operations involving SSL are _composed operations_. That is, there are intermediate completion handlers that touch the stream object prior to your final completion handler being called.   
These all make progress on the executor associated with your final completion handler (see bind_executor()). If you don't bind an executor to your handler, the stream's default executor is used.  
  
It therefore makes sense that this default executor (passed when the stream is created) is a copy of a strand, if you are in a multi-threaded environment.  
  
strands are cheaply-copied handle objects.
