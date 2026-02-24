# #2968 - HTTP's basic_parser_base::parse_target to allow empty targets (RFC 6455 - 3) [Closed]

> Username: manuelgustavo  
> Created at: 2025-01-07 10:20:27 UTC  
> Updated at: 2025-01-08 14:25:15 UTC  
> Closed at: 2025-01-08 14:25:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2968  

When testing a client's connection, I got the error `error::bad_target` in `basic_parser_base::parse_target`.   
  
Sample failed request (no path after GET, just two spaces):  
  
``` HTTP  
GET  HTTP/1.1\r\nHost: 127.0.0.1:25586\r\nUpgrade: websocket\r\nConnection: Upgrade\r\nSec-WebSocket-Key: 61yd0Ve4S6HYbtblMD0llw==\r\nSec-WebSocket-Version: 13\r\nSec-WebSocket-Protocol: pair1.sp.nanomsg.org\r\n\r\n"  
```  
  
In contrast to a regular request (containing "/" path after get):  
  
``` HTTP  
GET / HTTP/1.1\r\nHost: 127.0.0.1:25586\r\nConnection: Upgrade\r\nUpgrade: websocket\r\nSec-WebSocket-Version: 13\r\nSec-WebSocket-Key: N25ZlY2QErGtqbT2nzmGXA==\r\n\r\n"  
```  
  
Checking [RFC 6455](https://www.rfc-editor.org/rfc/rfc6455) - 3 / WebSocket URIs - It can be seen that the path should be `"/"` if the path component is empty.  
  
Suggested change:  
  
``` diff  
diff --git a/include/boost/beast/http/detail/basic_parser.ipp b/include/boost/beast/http/detail/basic_parser.ipp  
index 117fd36a..f3012d38 100644  
--- a/include/boost/beast/http/detail/basic_parser.ipp  
+++ b/include/boost/beast/http/detail/basic_parser.ipp  
@@ -334,8 +334,9 @@ parse_target(  
     }  
     if(it == first)  
     {  
-        // cannot be empty  
-        BOOST_BEAST_ASSIGN_EC(ec, error::bad_target);  
+        // RFC 6455 - 3.  WebSocket URIs - "/" if the path component is empty - https://www.rfc-editor.org/rfc/rfc6455  
+        result = "/";   
+        ++it;  
         return;  
     }  
     result = make_string(first, it++);  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-01-08 13:10:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2968#issuecomment-2577637148  

This is illegal according to the HTTP RFC

---

## Comment 2

> Username: manuelgustavo  
> Created at: 2025-01-08 14:24:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2968#issuecomment-2577799942  

Thanks for clarifying it. NNG is doing both client and server, so possibly it doesn't care about complying to other clients and servers.  
  
By the way, great library! It opens our minds to the possibilities of it together with ASIO.
