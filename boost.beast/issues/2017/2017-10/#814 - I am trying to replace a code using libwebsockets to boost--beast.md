# #814 - I am trying to replace a code using libwebsockets to boost::beast [Closed]

> Username: syedalamabbas  
> Created at: 2017-10-14 15:15:55 UTC  
> Updated at: 2017-10-15 23:53:21 UTC  
> Closed at: 2017-10-15 23:53:21 UTC  
> Url: https://github.com/boostorg/beast/issues/814  

Hi,   
        This is more of a feature request rather than a bug.  I have also posted this question on stack overflow [https://stackoverflow.com/questions/46726874/trying-to-replace-my-libwebsocket-code-with-boostbeast](). I want to find an equivalent call to lws_client_connect_via_info using beast.   
  
```  
		auto const host = "some_golang_local_server";  
		auto const port = "7350";  
		auto const text = "";  
  
		// The io_service is required for all I/O  
		boost::asio::io_service ios;  
  
		// These objects perform our I/O  
		tcp::resolver resolver{ ios };  
		websocket::stream<tcp::socket> ws{ ios };  
  
		// Look up the domain name  
		auto const lookup = resolver.resolve({ host, port });  
  
		// Make the connection on the IP address we get from a lookup  
		boost::asio::connect(ws.next_layer(), lookup);  
  
		// Perform the websocket handshake  
		ws.handshake(host, "/");  
```  
But while the code of the libwebconnect clears, beast gives an error at the handshake request. The connect passes.   
  
This line show:  
`std::cerr << "Error: " << e.what() << std::endl;`  
  
> Error: Websocket upgrade handshake failed.   
  
Is there some other settings that needs to be done for this to work? I found both sync and async examples follow the same initial setup and I have tried both using beast examples.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-14 16:06:31 UTC  
> Url: https://github.com/boostorg/beast/issues/814#issuecomment-336644941  

I replied here:  
https://stackoverflow.com/questions/46726874/trying-to-replace-my-libwebsocket-code-with-boostbeast

---

## Comment 2

> Username: syedalamabbas  
> Created at: 2017-10-15 23:53:21 UTC  
> Url: https://github.com/boostorg/beast/issues/814#issuecomment-336751257  

Resolved : [https://stackoverflow.com/questions/46726874/trying-to-replace-my-libwebsocket-code-with-boostbeast/]()
