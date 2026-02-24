# #813 - ssl websocket - URL path on write [Closed]

> Username: jimzag  
> Created at: 2017-10-12 21:58:00 UTC  
> Updated at: 2017-10-13 20:20:18 UTC  
> Closed at: 2017-10-13 20:20:17 UTC  
> Url: https://github.com/boostorg/beast/issues/813  

I am creating a websocket ssl client, and have the latest Beast code (ver 121), and using the sample code websocket_client_sync_ssl.  The server is expecting a request at this path:   
'wss://' + 'myapp.myserver.com' + ':5454/socket/server/config::asio?eventID=12345  
  
The following steps all appear to succeed but I have not been able to determine where to specify the this path: socket/server/config::asio?eventID=12345.  I will be sending the command data using ws.write(), which is failing right now which is not surprising since the needed path and param (eventID) are not being passed.  
This is probably quite a simple question but I have not been able to see where the URL/ params are specifed.  
  
        //Lookup the domain name  
	std::string const host = "myapp.myserver.com";  
	auto const lookup = resolver.resolve({ host, "5454" }, ec);  
  
        // Connect  
	boost::asio::connect(ws.next_layer().next_layer(), lookup);  
  
	// Perform the SSL handshake  
	ws.next_layer().handshake(ssl::stream_base::client, ec);  
  
Thanks   
Jim Z.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-12 22:47:22 UTC  
> Updated at: 2017-10-12 22:47:55 UTC  
> Url: https://github.com/boostorg/beast/issues/813#issuecomment-336299916  

```  
ws.handshake("myapp.myserver.com", "/socket/server/config::asio?eventID=12345");  
```  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/handshake/overload1.html  
:)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-12 23:34:48 UTC  
> Url: https://github.com/boostorg/beast/issues/813#issuecomment-336309747  

Note that when using `boost::asio::ssl::stream`, you have to perform **two handshakes**. First, the SSL protocol level handshake, and then the WebSocket handshake. Example:  
```  
// SSL handshake  
ws.next_layer().handshake(ssl::stream_base::client, ec);  
  
// WebSocket handshake  
ws.handshake("myapp.myserver.com", "/socket/server/config::asio?eventID=12345");  
```  
  
This is also illustrated in the example code snippet on this page:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/establishing_connections.html  
  
And in the WebSocket synchronous SSL client example:  
https://github.com/boostorg/beast/blob/develop/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp#L69  
  
Hope this helps!

---

## Comment 3

> Username: jimzag  
> Created at: 2017-10-13 20:20:17 UTC  
> Url: https://github.com/boostorg/beast/issues/813#issuecomment-336557521  

Thanks for the help!  The command is now successfully received by the websocket server.  I added the missing handshake at the websocket level after the ssl handshake.
