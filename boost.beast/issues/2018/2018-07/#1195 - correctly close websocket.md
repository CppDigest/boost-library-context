# #1195 - correctly close websocket [Closed]

> Username: quazeeee  
> Created at: 2018-07-17 06:43:52 UTC  
> Updated at: 2018-08-23 15:34:22 UTC  
> Closed at: 2018-08-23 15:34:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1195  

It's more a question then issue, but i need help. I implement in my code websocket client part with async read, but all other operations in sync style (connect, write, close). io_service run in separate thread and call event on message. Question: it's correct action when i close socket while in parallel thread async_read can read message from server? When i call close connection from client, server side connection closed successfully but client async_read fail with error (WebSocket connection failed due to a protocol violation). Should i stop iocontext before close or after call close? The same question for async_close, should i stop iocontext in onClose handler?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-17 13:38:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1195#issuecomment-405584759  

Hmmm... you can't mix asynchronous and synchronous websocket operations. I suggest you only use asynchronous.

---

## Comment 2

> Username: quazeeee  
> Created at: 2018-07-17 13:52:57 UTC  
> Updated at: 2018-07-17 13:53:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1195#issuecomment-405589748  

thank, your answer confirmed my experiments !

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-08-16 14:39:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1195#issuecomment-413568965  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-08-23 15:34:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1195#issuecomment-415461660  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
