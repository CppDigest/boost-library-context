# #1216 - [Question] How can I use beast with HTTP proxy? [Closed]

> Username: swr1bm86  
> Created at: 2018-08-06 12:11:08 UTC  
> Updated at: 2019-11-14 16:21:20 UTC  
> Closed at: 2018-09-13 07:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1216  

Hi, I want to create a beast WebSocket client with my local HTTP proxy, is there any documentation or verbose example to demonstrate how can I achieve that?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-06 12:37:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-410693363  

It is possible, but there are no examples. If you can paste some of the documentation for your proxy explaining what it expects in the initial handshake, I might be able to help.

---

## Comment 2

> Username: swr1bm86  
> Created at: 2018-08-06 14:10:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-410721024  

OK, currently my old system is written in 100% pure Python code, and we used the [python WebSocket library](https://github.com/websocket-client/websocket-client), I can set up HTTP proxy with that lib easily in [this way](https://github.com/websocket-client/websocket-client#http-proxy), so can I do this like a charm with beast? Thanks in advance :)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-06 14:48:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-410734208  

> can I do this like a charm with beast  
  
No, you will have to implement proxy support yourself. Beast is a low-level library, it only handles the serialization and parsing of the HTTP message.

---

## Comment 4

> Username: swr1bm86  
> Created at: 2018-08-07 05:41:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-410939668  

@vinniefalco OK, gotcha, thanks :)

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-09-06 06:39:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-418982005  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-09-13 07:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-420909272  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 7

> Username: allen2015555  
> Created at: 2019-01-01 11:07:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-450722579  

i want to create a wss client with beast and i want to use Fiddler to check the wss client is correct.i like the beast but it's regrettable it doesn't support proxy.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-01-01 13:17:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-450728426  

You can always implement proxy support yourself, use the HTTP interface to connect to the proxy first.

---

## Comment 9

> Username: swr1bm86  
> Created at: 2019-11-14 11:17:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-553842953  

@vinniefalco right now I want to implement this HTTP proxy feature, the right start point is to delegate the proxy options when we build HTTP requests here https://github.com/boostorg/beast/blob/0b68ed651b6bc7b681cf440ed6a220089e21473f/include/boost/beast/websocket/impl/stream_impl.hpp#L569-L595 which will be used by WebSocket client right?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-11-14 12:19:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-553863335  

If you want to modify the Upgrade request, then set a decorator on the stream:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_websocket/decorator.html

---

## Comment 11

> Username: swr1bm86  
> Created at: 2019-11-14 16:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1216#issuecomment-553963189  

make sense, the decorator is more flexible :)
