# #2960 - Question: How to keep alive websocket sptr session without activity [Closed]

> Username: fsmoke  
> Created at: 2024-12-06 14:34:14 UTC  
> Updated at: 2025-04-27 16:27:03 UTC  
> Closed at: 2025-04-27 16:27:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2960  

Hi.   
  
I am writing notification server. Every client connects to server just to handle some notifications. So there is no any read-write work to keep sptr of my session, just rare async_write operations.   
  
What async operation i must to do for fill the wasted time between async_writes?    
  
May be async_ping or pong, I don't really know much about what's under the hood of  websockes.   
  
But i cannot find any explicit examples about ping-pong in beast docs.   
  
Can anyone helps me?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-12-06 15:40:48 UTC  
> Updated at: 2024-12-06 15:41:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2960#issuecomment-2523522054  

Hi,  
You must always maintain an active `async_read` operation, as it handles ping/pong operations in the background. You just need to set proper timeout options:  
 [websocket::stream::set_option (2 of 3 overloads)](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option/overload2.html)  
[websocket::stream_base::timeout](https://www.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream_base__timeout.html)
