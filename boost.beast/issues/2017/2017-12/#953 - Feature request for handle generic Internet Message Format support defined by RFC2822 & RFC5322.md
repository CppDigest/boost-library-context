# #953 - Feature request for handle generic Internet Message Format support defined by RFC2822 & RFC5322 [Closed]

> Username: teeson  
> Created at: 2017-12-26 09:28:11 UTC  
> Updated at: 2018-01-02 06:01:35 UTC  
> Closed at: 2018-01-02 06:01:35 UTC  
> Url: https://github.com/boostorg/beast/issues/953  

I was developing a RTSP server which need to be tunneled over HTTP.  
the protocol is come from Apple.Inc. For detail please see [RTSP_Over_HTTP](http://www.opensource.apple.com/source/QuickTimeStreamingServer/QuickTimeStreamingServer-412.42/Documentation/RTSP_Over_HTTP.pdf)  
  
here is a example of that protocol:  
  
Client ‡ Server  
```  
GET /sw.mov HTTP/1.0  
User-Agent: QTS (qtver=4.1;cpu=PPC;os=Mac 8.6)  
x-sessioncookie: tD9hKgAAfB8ABCftAAAAAw  
Accept: application/x-rtsp-tunnelled  
Pragma: no-cache  
Cache-Control: no-cache  
```  
  
Server ‡ Client  
```  
HTTP/1.0 200 OK  
Server: QTSS/2.0 [v101] MacOSX  
Connection: close  
Date: Thu, 19 Aug 1982 18:30:00 GMT  
Cache-Control: no-store  
Pragma: no-cache  
Content-Type: application/x-rtsp-tunnelled  
```  
  
The tunnelled request as follow:  
  
```  
DESCRIBE rtsp://tuckru.apple.com/sw.mov RTSP/1.0  
CSeq: 1  
Accept: application/sdp  
Bandwidth: 1500000  
Accept-Language: en-US  
User-Agent: QTS (qtver=4.1;cpu=PPC;os=Mac 8.6)  
```  
  
The whole protocol is described in RFC 2326  
  
There are these questions:   
1. How can I handle  `x-sessioncookie` header field with  Beast, I don't understand why use enum to represent  a field name.  
2. How can I handle RTSP messages with Beast, I don't want to invent wheel for RTSP.  
3. the version field of RTSP cannot be properly processed by  Beast  
  
As the HTTP, RTSP, SIP or any other internet application protocol use the same format as  
Internet Message Format,  specified in RFC2822 & RFC5322.  
  
So I think a generic message  model is need and usefull.  
  
Thanks for your time and attention.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-27 16:43:48 UTC  
> Updated at: 2017-12-27 16:45:28 UTC  
> Url: https://github.com/boostorg/beast/issues/953#issuecomment-354140689  

>How can I handle x-sessioncookie header field with Beast  
  
Use a string:  
```  
request<empty_body> req;  
req.insert("X-SessionCookie", "ou812");  
```  
  
>...I don't understand why use enum to represent a field name.  
  
The use of an enumeration helps write code that is correct and concise. When using the enumeration, the compiler will let you know if you spell it wrong, by generating an error. But if you're using a string and you mis-type the field name, you will not get a warning. Instead, your program will simply malfunction, in a way that may or may not be easy to diagnose. HTTP allows any text for field names, so the enumerations can't cover all possibilities. But they cover over 250 of the common ones.  
  
>So I think a generic message model is need and usefull.  
  
Beast is designed for HTTP, not RTSP or SIP. Support for other protocols is something that might be explored some day but I would like the library to get more mature before exploring those options.

---

## Comment 2

> Username: teeson  
> Created at: 2017-12-28 03:17:44 UTC  
> Url: https://github.com/boostorg/beast/issues/953#issuecomment-354220883  

Thanks for your reply.  
I tried using Poco C++ Net Library to implement RTSP_Over_HTTP.  
They have a class MessageHeader to represent a common internet message.   
Did you know this library and compare it with Beast?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-28 03:33:48 UTC  
> Url: https://github.com/boostorg/beast/issues/953#issuecomment-354222309  

>Did you know this library and compare it with Beast?  
  
POCO does not use Boost.Asio and therefore, is not written for the networking interfaces which are on track to become part of the C++ Standard Library (currently it is a Technical Specification).  
  
See:  
http://cplusplus.github.io/networking-ts/draft.pdf

---

## Comment 4

> Username: teeson  
> Created at: 2017-12-28 03:52:33 UTC  
> Url: https://github.com/boostorg/beast/issues/953#issuecomment-354223884  

Ah. That's main design goal of Beast.  
By comparing the message models of both:   
Beast message<...>::version()  returns unsigned.   
POCO HTTPMessage::getVersion() returns string like "HTTP/1.1".  This make POCO more flexible to be possible to handle RTSP or other protocols.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-12-31 19:11:30 UTC  
> Url: https://github.com/boostorg/beast/issues/953#issuecomment-354619631  

Has this issue been resolved?
