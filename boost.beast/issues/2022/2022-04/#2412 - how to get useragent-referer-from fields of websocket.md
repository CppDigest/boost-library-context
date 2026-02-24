# #2412 - how to get useragent/referer/from fields of websocket [Closed]

> Username: RavikumarTulugu  
> Created at: 2022-04-24 07:38:13 UTC  
> Updated at: 2022-04-24 14:37:01 UTC  
> Closed at: 2022-04-24 14:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2412  

how to access useragent/referer/from fields on a websocket .

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-04-24 08:13:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2412#issuecomment-1107787890  

Use the alternate form of async_accept. You provide a request object, which is filled in during the accept operation.  
  
https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html  
  
You can get the header fields from there.

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2022-04-24 10:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2412#issuecomment-1107808642  

Thank you for the reply. This is very important for us.

---

## Comment 3

> Username: RavikumarTulugu  
> Created at: 2022-04-24 12:12:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2412#issuecomment-1107828841  

it would be great if you can point me to a working code. The req parameter in the async_accept is supposed to be both in out ?? i was giving an empty req uninitialized and my accept started failing. surely, my usage is wrong.  I started seeing this error .   
"The WebSocket handshake method was not GET". i commented out for now and reverted back.

---

## Comment 4

> Username: sehe  
> Created at: 2022-04-24 12:49:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2412#issuecomment-1107835353  

I have a complete example here: https://stackoverflow.com/a/71246212/85371

---

## Comment 5

> Username: RavikumarTulugu  
> Created at: 2022-04-24 14:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2412#issuecomment-1107854366  

Thanks for the code, it worked fine.
