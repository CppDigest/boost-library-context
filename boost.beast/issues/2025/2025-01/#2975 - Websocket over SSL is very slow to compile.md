# #2975 - Websocket over SSL is very slow to compile [Open]

> Username: pfeatherstone  
> Created at: 2025-01-22 08:49:08 UTC  
> Updated at: 2025-03-10 11:55:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2975  

I'm writing and HTTPS server with WSS support. I've noticed that it's extremely slow to compile and generates a very bloated binary.  
I've tried #including only what I need from both Asio and Beast but that makes no difference.   
HTTPS on its own is fine. HTTP + WS is fine. But HTTPS + WSS is mega slow to compile (like 30s to compile a single file)  
Anybody noticed this before?  
@vinniefalco what's the state of https://github.com/cppalliance/http_proto and https://github.com/cppalliance/ws_proto ?  
I imagine you eventually want to replace Beast with your suite of "sans-io" libraries that sit on top of Asio right?

---

## Comment 1

> Username: KaiPetzke  
> Created at: 2025-03-10 11:55:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2975#issuecomment-2710337545  

I have noticed, too, that websocket apps take a long time to compile and create large object files. I have not tried websockets without SSL, so I always thought, that a mega complex web server/client app including HTTP/2, Cookies and a lot of the other fancy stuff was generated and that's why it is slow. My solution was always to factor the actual websocket server or client into its own source module, that isn't touched so often.  
  
Looking at `nm --size --demangle websocket.o`, it seems, that a lot of the produced code is template bloat. 1508 symbols with a total size of over 0.5 MB have "::read_some_op" in their name. And even over 3700 symbols with over 1.0 MB operate on some "::handshake_op". These figures are for G++, but CLANG++ delivers basically the same result.
