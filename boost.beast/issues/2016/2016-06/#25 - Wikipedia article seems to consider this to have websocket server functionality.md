# #25 - Wikipedia article seems to consider this to have websocket server functionality [Closed]

> Username: ignasg  
> Created at: 2016-06-15 13:34:24 UTC  
> Updated at: 2016-07-08 00:44:07 UTC  
> Closed at: 2016-07-08 00:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/25  

https://en.wikipedia.org/wiki/Comparison_of_WebSocket_implementations  
As I only see this is a client library, probably the wikipedia article needs to be updated

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-06-15 15:16:33 UTC  
> Url: https://github.com/boostorg/beast/issues/25#issuecomment-226219881  

Where did you get the idea that Beast only offers a WebSocket client? It has both. We use Beast in rippled, which exposes a WebSocket server for clients to issue requests:  
https://github.com/ripple/rippled/blob/develop/src/ripple/server/impl/SSLWSPeer.h#L48  
  
To implement a server, just call `beast::websocket::stream::accept` on the socket used to receive the incoming connection, and it will read the Upgrade request and respond to it appropriately:  
https://github.com/vinniefalco/Beast/blob/999e2fa0318b5982736d3ea01a418770ea802671/include/beast/websocket/stream.hpp#L312  
  
The Beast Autobahn|Testsuite report shows both the synchronous and asynchronous modes of operation, source code for those WebSocket servers are here:  
https://github.com/vinniefalco/Beast/blob/999e2fa0318b5982736d3ea01a418770ea802671/test/websocket/websocket_async_echo_peer.hpp  
https://github.com/vinniefalco/Beast/blob/999e2fa0318b5982736d3ea01a418770ea802671/test/websocket/websocket_sync_echo_peer.hpp  
  
Hope this helps!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-07-08 00:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/25#issuecomment-231247745  

I'm going to close this since it is not a bug
