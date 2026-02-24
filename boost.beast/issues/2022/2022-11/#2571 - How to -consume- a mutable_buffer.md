# #2571 - How to "consume" a mutable_buffer [Closed]

> Username: benstadin  
> Created at: 2022-11-30 21:35:54 UTC  
> Updated at: 2022-12-01 15:47:34 UTC  
> Closed at: 2022-12-01 15:47:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2571  

I want to replace a flat_buffer with a mutable_buffer in order to get access to the read_some interface for controlling my websocket sessions.  
  
How to consume / clear a mutable_buffer? Or must it be recreated?   
  
And related, how to get a string from a mutable_buffer, since buffers_to_string does not accept a mutable_buffer?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-11-30 23:27:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2571#issuecomment-1332871741  

`flat_buffer` is not the same as `mutable_buffer`. One is a dynamic buffer the other is like a span.

---

## Comment 2

> Username: benstadin  
> Created at: 2022-12-01 15:47:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2571#issuecomment-1333970781  

Thanks. I've adapted my code accordingly.

---

## Comment 3

> Username: benstadin  
> Created at: 2022-12-01 15:47:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2571#issuecomment-1333970983  

Thanks. I've adapted my code accordingly.
