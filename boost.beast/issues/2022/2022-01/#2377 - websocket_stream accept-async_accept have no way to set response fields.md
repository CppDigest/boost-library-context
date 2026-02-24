# #2377 - websocket_stream  accept/async_accept have no way to set response fields. [Closed]

> Username: microcai  
> Created at: 2022-01-15 14:17:57 UTC  
> Updated at: 2022-01-16 03:39:43 UTC  
> Closed at: 2022-01-16 03:39:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2377  

for regular http response, async_write accept a response argument.  
for websocket  accept, which in-turn will send a 101 response, have no way to set additional header fileds.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-01-15 15:20:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2377#issuecomment-1013699385  

https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/using_websocket/decorator.html

---

## Comment 2

> Username: microcai  
> Created at: 2022-01-16 03:39:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2377#issuecomment-1013804236  

That's too complicated ...  
anyway, thanks!
