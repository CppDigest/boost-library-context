# #1295 - The WebSocket handshake was declined by the remote peer [Closed]

> Username: jingliming  
> Created at: 2018-11-10 09:28:16 UTC  
> Updated at: 2018-11-11 04:28:45 UTC  
> Closed at: 2018-11-11 04:28:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1295  

hello,  
i use this command ./websocket-client-sync 10.103.101.158 13578 "hello"  
and one error occured "The WebSocket handshake was declined by the remote peer"   
i capture http package, wbesocket server responed "http/1.1 404 not found"  
websocket server is unuse beast websocket server  
how to fixed it?  
tks

---

## Comment 1

> Username: jingliming  
> Created at: 2018-11-10 12:57:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1295#issuecomment-437582084  

the question is solved

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-11-10 13:41:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1295#issuecomment-437584612  

"Not Found" means that the URI was not recognized by the server

---

## Comment 3

> Username: jingliming  
> Created at: 2018-11-11 04:28:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1295#issuecomment-437643011  

> "Not Found" means that the URI was not recognized by the server  
  
that's true,tks
