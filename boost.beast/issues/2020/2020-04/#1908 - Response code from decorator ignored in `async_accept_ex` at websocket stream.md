# #1908 - Response code from decorator ignored in `async_accept_ex` at websocket stream. [Closed]

> Username: onto  
> Created at: 2020-04-20 10:07:54 UTC  
> Updated at: 2020-04-20 20:15:12 UTC  
> Closed at: 2020-04-20 20:15:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1908  

Since that old commit https://github.com/boostorg/beast/commit/cc43b46c4287b0dbd9b668ec9ee0221ad0ce2043#diff-5639fbec6c0cb9cf08011ff3eb305ee5L123 i've found that response code not check after decorator, so it not possible now to reject upgrade request by additional conditions. So is it a bug or considered decision?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-20 10:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1908#issuecomment-616451758  

If you want to reject the handshake your are supposed to read it yourself using Beast's HTTP functions, and then respond appropriately:  
https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests

---

## Comment 2

> Username: onto  
> Created at: 2020-04-20 20:15:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1908#issuecomment-616783989  

Not so easy like one decorator, but works.
