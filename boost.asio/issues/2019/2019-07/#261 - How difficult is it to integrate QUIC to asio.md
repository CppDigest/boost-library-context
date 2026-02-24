# #261 - How difficult is it to integrate QUIC to asio? [Closed]

> Username: Warchant  
> Created at: 2019-07-18 18:28:53 UTC  
> Updated at: 2025-12-16 13:42:49 UTC  
> Closed at: 2019-11-01 15:17:01 UTC  
> Url: https://github.com/boostorg/asio/issues/261  

The question may be silly, but how difficult is it to integrate QUIC to asio?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-01 15:11:49 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-548824929  

It is no more difficult than integrating it into any other platform-specific or portable sockets. The heavy lifting is in implementing the actual QUIC specification, which is enormous. Please close this issue.

---

## Comment 2

> Username: heretic13  
> Created at: 2023-02-14 19:58:33 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-1430300537  

https://github.com/cbodley/nexus

---

## Comment 3

> Username: goforit22123-netizen  
> Created at: 2025-12-11 10:46:46 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3641332210  

@vinniefalco OpenSSL supports QUIC now. Is there a plan to add QUIC support to asio?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2025-12-11 17:17:47 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3642931711  

its not up to me but we are exploring using quiche in beast2

---

## Comment 5

> Username: goforit22123-netizen  
> Created at: 2025-12-12 09:13:18 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3645606587  

@vinniefalco It would be great if they added QUIC support to Asio and HTTP2/http3 to Beast. Thanks for the library, but Boost is lagging far behind the modern internet.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2025-12-12 18:12:47 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3647604810  

No one needs QUIC or HTTP3 except big tech companies, and they ain't using Beast. What is your use-case?

---

## Comment 7

> Username: goforit22123-netizen  
> Created at: 2025-12-13 11:35:04 UTC  
> Updated at: 2025-12-13 11:57:54 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3649241098  

@vinniefalco  I disagree with you. Suppose I want to implement an HTTP/3 client that needs to access resources provided by a major tech company over HTTP/3. How can this be done without using QUIC and HTTP/3 modules? Or.. If I want write something like sniffer or api testing software? Or... maybe i need some custom and lightweight webserver with http/3 support? All this stuff needs quic & http3.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2025-12-13 19:06:29 UTC  
> Updated at: 2025-12-13 19:09:15 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3649718372  

Every "big tech" endpoint that supports HTTP/3 also supports HTTP/1. Show me the endpoint that you are trying to use which does not allow HTTP/1 over TLS. And if you want to write a sniffer or api tester, or lightweight webserver for http/3 there are already existing libraries.

---

## Comment 9

> Username: goforit22123-netizen  
> Created at: 2025-12-14 10:15:20 UTC  
> Updated at: 2025-12-14 10:36:43 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3650661816  

@vinniefalco   
  
1) Yes, you can use fallback to HTTP/1, but it's not just about receiving data, but also about the advantages of HTTP/3, since data can be loaded faster using this protocol. This may be important for programs like "Download Manager"  
2) Yes, there are libraries, but they are not as convenient as asio/beast. In general, almost everything that exists in boost can be replaced with other libraries, but we use boost because it is convenient and reliable.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2025-12-14 19:32:57 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3651877270  

Are you implementing Download Manager? What is your use-case?

---

## Comment 11

> Username: goforit22123-netizen  
> Created at: 2025-12-15 10:09:14 UTC  
> Updated at: 2025-12-15 10:11:17 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3654819103  

@vinniefalco No, I'm not developing a Download Manager, but I'd love for boost to be a library that could be used with modern protocols. That would be really cool! Moreover, it shouldn't be that difficult. OpenSSL supports QUIC on both the client and server. Boost already uses OpenSSL. As for HTTP/2 and HTTP/3, there are also MIT-licensed libraries for them. Why not add a convenient interface for them to boost/beast?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2025-12-15 13:55:12 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3655773043  

If its not that difficult, why don't you consider submitting a pull request?

---

## Comment 13

> Username: goforit22123-netizen  
> Created at: 2025-12-16 11:41:40 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3660112099  

@vinniefalco At least because I'm not familiar with the internals of ASIO. However, you previously said that "The heavy lifting is in implementing the actual QUIC specification, which is enormous." Now this problem is solved, OpenSSL has already implemented QUIC, it's time to add its support to asio, but I'm just drawing your attention to this, it's up to you to decide, of course.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2025-12-16 13:42:49 UTC  
> Url: https://github.com/boostorg/asio/issues/261#issuecomment-3660588068  

I'm not the maintainer of Asio and this issue is not in the right repository. You should be asking here: https://github.com/chriskohlhoff/asio/issues/1691
