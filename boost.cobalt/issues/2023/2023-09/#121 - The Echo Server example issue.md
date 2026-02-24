# #121 - The Echo Server example issue [Closed]

> Username: akrzemi1  
> Created at: 2023-09-27 06:10:58 UTC  
> Updated at: 2023-09-29 08:59:03 UTC  
> Closed at: 2023-09-29 08:59:02 UTC  
> Url: https://github.com/boostorg/cobalt/issues/121  

When I try the echo server example on MSVC, it echoes every letter separately: I want to type (form terminal, using telnet) a word, but upon every single letter it echoes that letter alone. So I get   
  
```  
wwoorrlldd  
  
  
```  
instead of  
  
```  
world  
world  
```  
  
Is that intentional? I recall that the coroutine example from ASIO worked as I expect.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-28 03:40:02 UTC  
> Url: https://github.com/boostorg/cobalt/issues/121#issuecomment-1738388318  

Not intentional, but correct if you use telnet. The echo server just sends back the packages you sent, so if telnet sends them individually, that's correct.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2023-09-29 08:59:02 UTC  
> Url: https://github.com/boostorg/cobalt/issues/121#issuecomment-1740548297  

Thanks.
