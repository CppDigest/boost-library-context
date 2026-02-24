# #1901 - [Bug] Websocket chat server fails to call `.expires_never()` on TCP stream before upgrading [Closed]

> Username: cmazakas  
> Created at: 2020-04-13 15:14:40 UTC  
> Updated at: 2020-04-14 01:33:03 UTC  
> Closed at: 2020-04-14 01:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1901  

As outlined here: https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/using_websocket/timeouts.html  
  
One must first disable the timeouts on the underlying `beast::tcp_stream` before upgrading it to a websocket.  
  
Looking at the code here:  
https://www.boost.org/doc/libs/1_72_0/libs/beast/example/websocket/server/chat-multi/http_session.cpp  
  
and here:  
https://www.boost.org/doc/libs/1_72_0/libs/beast/example/websocket/server/chat-multi/websocket_session.cpp  
  
The required `.expires_never()` call never seems to be made.
