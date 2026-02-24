# #2164 - Question: Does Beast support 3xx Websocket redirect? [Closed]

> Username: lmish001  
> Created at: 2021-02-12 14:51:58 UTC  
> Updated at: 2022-08-10 20:33:53 UTC  
> Closed at: 2022-01-09 05:17:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2164  

Hello,   
  
I wanted to know if Beast supports 3xx Websocket redirect. I have done some tests where I try to redirect a websocket connection, using Beast 1.70, but I receive the following error:   
  
`code: 20, message: The WebSocket handshake was declined by the remote peer, cat name: boost.beast.websocket`  
  
Is this due to an incorrect redirect or is this not currently supported by Beast?  
  
Thank you in advance!  
  
Liza

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-12 15:13:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-778253655  

Hi there,   
  
It's not automatic but it's entirely possible.  
  
What you would have to do is use the HTTP interface to follow the redirect and then use the response that contains the upgrade confirmation in the second form of the websocket handshake:  
  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_handshake/overload2.html  
  
If you'd like me to knock up a demo just let me have a URL that you're trying to connect to and I'll add a program to my demo suite.

---

## Comment 2

> Username: lmish001  
> Created at: 2021-02-15 13:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-779212667  

Hi,  
Thank you for the quick reply! I can't share the URL I'm trying to connect to, but it's helpful to know that it's possible to implement url redirects.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-850857879  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1008232380  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 5

> Username: benstadin  
> Created at: 2022-08-08 18:00:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1208436545  

I am looking for the same. Could you maybe provide a code demo, or add this feature to the existing WebSocket client example?

---

## Comment 6

> Username: madmongo1  
> Created at: 2022-08-08 18:26:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1208462230  

> I am looking for the same. Could you maybe provide a code demo, or add this feature to the existing WebSocket client example?  
  
Happy to write one that uses coroutines, if that helps?

---

## Comment 7

> Username: benstadin  
> Created at: 2022-08-08 18:33:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1208468300  

Thanks for the quick response. For me it would be better without coroutines, as I use Boost header-only. Though if the code can be adapted easily, I'm happy with what I can get;).

---

## Comment 8

> Username: madmongo1  
> Created at: 2022-08-09 06:55:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1208986255  

OK, I'll write the code and then use it as the material for a blog post. I think it's an interesting enough topic

---

## Comment 9

> Username: madmongo1  
> Created at: 2022-08-09 13:40:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1209396862  

Are you able to provide me with the URL of a webserver that I can use to test?

---

## Comment 10

> Username: madmongo1  
> Created at: 2022-08-09 21:17:58 UTC  
> Updated at: 2022-08-09 21:18:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1209905771  

Please see this example:  
https://github.com/madmongo1/blog-2022-Aug-websock-redirect  
  
This demonstration program will:  
 - create a mini webserver that will keep redirecting a client until it arrives at a url that can serve websockets.  
 - create a client which seeks to connect a websocket while obeying redirects.  
 - shut down the server after a successful conversation over websockets or if there have been too many redirects.  
 Features:  
- client websocket connection supports ws and wss protocols  
- server requests arriving by http will be redirected to https  
- client can redirect between http and https up to a configured limit number of times.  
  
Any questions, feel free to reach out.  
Note: uses features inroduced in boost.asio 1.80. Notably the fact that the result of a `deferred` completion token is now awaitable.

---

## Comment 11

> Username: madmongo1  
> Created at: 2022-08-09 21:21:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1209908452  

example output:  
```  
Initialising  
server starting  
enter: comain()  
attempting connection: ws://127.0.0.1:36057/givemeawebsocket/  
...handshake  
...error: The WebSocket handshake was declined by the remote peer  
HTTP/1.1 301 Moved Permanently  
Location: wss://127.0.0.1:38421/websocket-5  
Connection: close  
Content-Length: 54  
  
attempting connection: wss://127.0.0.1:38421/websocket-5  
...handshake  
...error: The WebSocket handshake was declined by the remote peer  
HTTP/1.1 301 Moved Permanently  
Location: wss://127.0.0.1:38421/websocket-4  
Connection: close  
Content-Length: 54  
  
attempting connection: wss://127.0.0.1:38421/websocket-4  
...handshake  
...error: The WebSocket handshake was declined by the remote peer  
HTTP/1.1 301 Moved Permanently  
Location: wss://127.0.0.1:38421/websocket-3  
Connection: close  
Content-Length: 54  
  
attempting connection: wss://127.0.0.1:38421/websocket-3  
...handshake  
...error: The WebSocket handshake was declined by the remote peer  
HTTP/1.1 301 Moved Permanently  
Location: wss://127.0.0.1:38421/websocket-2  
Connection: close  
Content-Length: 54  
  
attempting connection: wss://127.0.0.1:38421/websocket-2  
...handshake  
...error: The WebSocket handshake was declined by the remote peer  
HTTP/1.1 301 Moved Permanently  
Location: wss://127.0.0.1:38421/websocket-1  
Connection: close  
Content-Length: 54  
  
attempting connection: wss://127.0.0.1:38421/websocket-1  
...handshake  
...error: The WebSocket handshake was declined by the remote peer  
HTTP/1.1 301 Moved Permanently  
Location: wss://127.0.0.1:38421/websocket-0  
Connection: close  
Content-Length: 54  
  
attempting connection: wss://127.0.0.1:38421/websocket-0  
...handshake  
...success  
HTTP/1.1 101 Switching Protocols  
Upgrade: websocket  
Connection: upgrade  
Sec-WebSocket-Accept: dv+BXBDUc/7Bhtj1YXe6gz85FOA=  
Server: Boost.Beast/330  
  
Hello, World!  
sever exception: multiple exceptions  
https_server - exception: stream truncated [asio.ssl.stream:1]  
Finished  
```

---

## Comment 12

> Username: madmongo1  
> Created at: 2022-08-10 06:40:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1210229209  

@benstadin  @lmish001

---

## Comment 13

> Username: benstadin  
> Created at: 2022-08-10 16:49:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1210983425  

@madmongo1 This looks elegant, thank you very much! I also do not have a server yet to test with, because this is an issue that sometimes pops up for some of our clients within their own environment. Having a server to test with saves me quite some headache as well.  
Does it require C++20?  
I will have to wait a bit until Boost 1.80 is released in order to test it in our product.   
  
Is the blog article you mentioned available online? I would be interested to understand some of the details behind the scenes.

---

## Comment 14

> Username: madmongo1  
> Created at: 2022-08-10 17:02:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1210999112  

@benstadin I am waiting for the blog entry PR to be merged into the web site, but in the meantime you can have a look at the preview:  
  
https://142.cppalliance.prtest.cppalliance.org/richard/2022/08/10/RichardsAugustUpdate.html

---

## Comment 15

> Username: madmongo1  
> Created at: 2022-08-10 17:02:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1211000232  

You can download boost-1.80.0.beta1 today from the boost web site:  
https://boostorg.jfrog.io/artifactory/main/beta/1.80.0.beta1/source/

---

## Comment 16

> Username: madmongo1  
> Created at: 2022-08-10 20:33:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2164#issuecomment-1211239800  

@benstadin live now: https://cppalliance.org/richard/2022/08/10/RichardsAugustUpdate.html
