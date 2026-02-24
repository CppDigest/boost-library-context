# #1172 - in beast, is websocket stream or ws.async_read() will get all the data ws.send()? [Closed]

> Username: opengpu  
> Created at: 2018-06-29 01:11:58 UTC  
> Updated at: 2018-09-04 15:41:13 UTC  
> Closed at: 2018-09-04 15:41:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1172  

in beast, is websocket stream or ws.async_read() will get all the data ws.send()? no matter how big the dataSize is...  
thank you

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-29 01:19:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-401219379  

`websocket::stream::async_read` can read any size message, but the default limits messages to 16 megabytes, see:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/read_message_max/overload1.html

---

## Comment 2

> Username: opengpu  
> Created at: 2018-06-29 02:09:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-401226222  

so for WebSocket, one ws.async_read() will read the entire data of the ws.send() by other client?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-29 04:12:54 UTC  
> Updated at: 2018-06-29 04:13:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-401242336  

> one ws.async_read() will read the entire data of the ws.send() by other client?  
  
Yes.

---

## Comment 4

> Username: opengpu  
> Created at: 2018-06-29 06:33:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-401261469  

ok,great! thank you!  
so what is the usage of async_read_some?is it equal to devide one async_read into two or more async_read_some? eg. 1st async_read_some read 8 bytes, 2nd async_read_some read the left bytes.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-07-29 07:26:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-408657702  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-07-29 14:04:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-408680004  

> so what is the usage of async_read_some?is it equal to devide one async_read into two or more async_read_some  
  
Yes

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-08-28 14:43:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-416612860  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-09-04 15:41:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1172#issuecomment-418416375  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
