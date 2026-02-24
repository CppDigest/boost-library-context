# #1302 - Does beast already support HTTP2? [Closed]

> Username: pengweichu  
> Created at: 2018-11-14 14:33:19 UTC  
> Updated at: 2025-05-26 01:27:21 UTC  
> Closed at: 2018-11-28 17:17:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1302  

I would like to use beast to send the push notifications to Apple Push Notification Service, it requires the HTTP2(https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CommunicatingwithAPNs.html#//apple_ref/doc/uid/TP40008194-CH11-SW1) support, my question is does beast is support HTTP2 now ?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-14 14:46:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-438686509  

No

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-11-28 17:17:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-442529372  

It is unlikely that Beast will ever support HTTP/2, as there is now a published HTTP/3 specification (based on QUIC).

---

## Comment 3

> Username: rustyx  
> Created at: 2019-02-12 12:31:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-462741848  

The proposed HTTP/3 can't be the reason for not wanting to support HTTP/2 (which works today in all major browsers already).  
What is the real reason?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-12 13:47:24 UTC  
> Updated at: 2019-02-12 13:50:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-462765237  

HTTP/2 (and to some extent HTTP/3) is difficult and complex to implement, probably more difficult than it was to write all of beast in the first place. The amount of value that comes out of implementing a new HTTP version is relatively low compared to the other things I could be working on now (such as automatic websocket timeouts or more multi-threaded server examples).  
  
It would very likely take over a year of focused effort to have HTTP/3, so I have to carefully choose what to work on in order to generate the most value since I am just one person working on this. Now, it doesn't mean that HTTP.Next will never happen, but I have other more high priority things to take care of for now.  
  
Nothing stops someone from building a new HTTP/2 or HTTP/3 library from Boost.Asio's networking APIs, and using Beast's `http::message` container (can't use the parser and serializer obviously, or the stream algorithms). They could also use Beast's core functionality such as the composed operation helpers and various buffer types and algorithms. Such a library could interoperate nicely with other libraries that use Beast - HTTP/1, HTTP/2, and HTTP/3 messages are the same and could use the same Beast container.  
  
Perhaps you could write it?

---

## Comment 5

> Username: ckaminski  
> Created at: 2020-12-13 01:31:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-743931951  

If you're not partial to windows (where it won't build natively yet), nghttp2 already provides libnghttp2_asio.    
  
I've built it with cygwin and in docker containers on Windows.   Works great on Linux and Mac.  *shrug*

---

## Comment 6

> Username: patlecat  
> Created at: 2021-01-05 15:18:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-754700136  

>   
>   
> If you're not partial to windows (where it won't build natively yet), nghttp2 already provides libnghttp2_asio.  
>   
> I've built it with cygwin and in docker containers on Windows. Works great on Linux and Mac. _shrug_  
  
It seems this lib has C++ already integrated and ready to use as you can see here in this file: https://github.com/nghttp2/nghttp2/blob/master/src/nghttpd.cc

---

## Comment 7

> Username: patlecat  
> Created at: 2021-01-05 15:20:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-754701366  

> It is unlikely that Beast will ever support HTTP/2, as there is now a published HTTP/3 specification (based on QUIC).  
So you planned to add HTTP/3 soon? Do you have a timeline for this?

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-01-05 19:58:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-754864544  

I don’t think we have plans to include http3 into beast.  
I don’t think we’ve seen a compelling motivation yet.

---

## Comment 9

> Username: patlecat  
> Created at: 2021-01-05 20:19:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-754875655  

@madmongo1 Then could nghttp2 be adapted to be used with Beast? It seems they also use asio but do not require it. Have you looked at this option?

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-01-05 20:22:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-754877016  

I haven’t looked into nghttp2 as I’m not a maintainer of it. Happy to offer help if the maintainers would like to use beast.

---

## Comment 11

> Username: jeff-martens  
> Created at: 2023-07-12 19:13:25 UTC  
> Updated at: 2023-07-12 19:14:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-1633070500  

Compelling reasons are:  
- talking to services that only support HTTP/2+   
- improving performance by largely removing connection setup time  
- simpler connection management  
  
The lack of support for HTTP/2+ and the lack of support for connection pooling makes Beast irrelevant for use in many microservice architectures.  From my perspective Beast should be considered as an end of life maintenance project at this point.    Fine for existing uses, but should not be considered for new projects.

---

## Comment 12

> Username: nqf  
> Created at: 2025-05-09 06:45:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-2865339041  

Now Rust's HTTP libraries support http2 and 3, and I sincerely hope that Beast can also support them

---

## Comment 13

> Username: patlecat  
> Created at: 2025-05-23 13:07:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-2904366358  

@nqf I gave up hope years ago. Better use nghttp2 for this if you need modern protocol support.

---

## Comment 14

> Username: nqf  
> Created at: 2025-05-26 01:27:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1302#issuecomment-2908261391  

> [@nqf](https://github.com/nqf) I gave up hope years ago. Better use nghttp2 for this if you need modern protocol support.  
  
I found this simple packaging https://github.com/fantasy-peak/simple_http#
