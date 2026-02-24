# #948 - Better documentation for reading websocket requests [Closed]

> Username: vinniefalco  
> Created at: 2017-12-23 14:57:47 UTC  
> Updated at: 2024-02-14 06:12:01 UTC  
> Closed at: 2024-02-14 06:12:01 UTC  
> Url: https://github.com/boostorg/beast/issues/948  

The documentation for websocket should title this section "Inspecting the Upgrade Request" and include specific language explaining how the user may retrieve and inspect the HTTP upgrade message:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests

---

## Comment 1

> Username: ashtum  
> Created at: 2024-02-14 06:12:01 UTC  
> Url: https://github.com/boostorg/beast/issues/948#issuecomment-1943143513  

Addressed in https://github.com/boostorg/beast/commit/ab9a4c66e0cae58c7ded86224491545f3197cf5b.
