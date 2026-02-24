# #3012 - Its just a question about websocket::stream::async_handshake [Open]

> Username: gitGameStory  
> Created at: 2025-05-28 20:08:10 UTC  
> Updated at: 2025-05-30 16:39:27 UTC  
> Url: https://github.com/boostorg/beast/issues/3012  

Im not sure if i may ask here..  
I have 2 of them.  
  
1. Whats is websocket state In case of error in async_handshake? Can i reuse it (call async_resolve and async_connect right after it callbacks with error) ? Or should i call some kind of close and only then call async_resolve ?  
2. Can i close socket not gracefully (just shutdown it) without breaking its state ?  
  
Also i have couple of other questions of advanced_server_flex.cpp example.  
  
Im writing a browser game with c++ compiled to wasm (it is a binary that runs in browser)  
So i want to have server written with c++ too. But have lack of experience with asio, but i have good exp with  async IOCP (win version of epoll) so i have just minor questions, but need to be sure 100% of how exact things are working.  
  
Thanx in advance guys!

---

## Comment 1

> Username: ashtum  
> Created at: 2025-05-30 16:39:26 UTC  
> Url: https://github.com/boostorg/beast/issues/3012#issuecomment-2922861097  

> 1. Whats is websocket state In case of error in async_handshake? Can i reuse it (call async_resolve and async_connect right after it callbacks with error) ? Or should i call some kind of close and only then call async_resolve ?  
  
No, you cannot reuse a connection after a failed handshake, as specified by RFC 6455:  
> If the server's response does not conform to the requirements for the  
   server's handshake as defined in this section and in [Section 4.2.2](https://datatracker.ietf.org/doc/html/rfc6455#section-4.2.2),  
   the client MUST _Fail the WebSocket Connection_.  
  
> 2. Can i close socket not gracefully (just shutdown it) without breaking its state ?>   
  
Yes, you can close a connection non-gracefully, but keep in mind that this may result in lost messages on the receiving side, as the operating system may not have sent the entire buffer yet and could discard it.  
  
  
> Also i have couple of other questions of advanced_server_flex.cpp example.  
>   
> Im writing a browser game with c++ compiled to wasm (it is a binary that runs in browser) So i want to have server written with c++ too. But have lack of experience with asio, but i have good exp with async IOCP (win version of epoll) so i have just minor questions, but need to be sure 100% of how exact things are working.  
>   
> Thanx in advance guys!  
  
You can begin by exploring examples from the Asio and Beast projects. Using Asio for network programming is often much easier and safer than working directly with low-level APIs.
