# #2332 - ssl_handshake: tlsv1 alert internal error [Closed]

> Username: tzzza  
> Created at: 2021-10-23 20:08:37 UTC  
> Updated at: 2023-12-31 10:17:20 UTC  
> Closed at: 2023-12-31 10:17:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2332  

from beast/example/websocket/client:  
  
"websocket_client_async_ssl" example fails ssl_handshake with error: "ssl_handshake: tlsv1 alert internal error"  
  
while at the same time the "websocket_client_sync_ssl" example succeeds at same server.  
  
OS: Windows 10  
Boost: 1.77.0 BOOST_BEAST_VERSION 318  
OpenSSL: 1.1.1l  24 Aug 2021  
CPP: Microsoft (R) C/C++ Optimizing Compiler Version 19.29.30136 for x64

---

## Comment 1

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2332#issuecomment-1008220715  

This issue has been open for a while with no activity, has it been resolved?
