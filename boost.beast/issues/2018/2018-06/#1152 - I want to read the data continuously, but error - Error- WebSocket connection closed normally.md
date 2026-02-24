# #1152 - I want to read the data continuously, but error : Error: WebSocket connection closed normally. [Closed]

> Username: weiweiloong  
> Created at: 2018-06-07 02:47:17 UTC  
> Updated at: 2018-06-07 11:51:05 UTC  
> Closed at: 2018-06-07 11:51:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1152  

hello, first thank you , The previous question is Ok .   
new question:  
I want to read the data continuously, but error :  
Error: WebSocket connection closed normally.  
  
code: beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp  
  
		auto host = "stream.binance.com";  
		auto port = "9443";  
		auto text = "ws/bnbbtc@depth";  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
        // The SSL context is required, and holds certificates  
        ssl::context ctx{ssl::context::sslv23_client};  
		std::string filename = "./cacert.pem";  
		ctx.set_verify_mode(ssl::verify_none);  
		//ctx.load_verify_file(filename);  
		  
        tcp::resolver resolver{ioc};  
        websocket::stream<ssl::stream<tcp::socket>> ws{ioc, ctx};  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
		boost::asio::connect(ws.next_layer().next_layer(), results.begin(), results.end());  
  
        // Perform the SSL handshake  
        ws.next_layer().handshake(ssl::stream_base::client);  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/ws/bnbbtc@depth");  
  
        // Send the message  
        ws.write(boost::asio::buffer(std::string(text)));  
  
        // This buffer will hold the incoming message  
        boost::beast::multi_buffer b;  
  
        // Read a message into our buffer  
        while( 1 )  
        {  
        	sleep(1);  
        	ws.read(b);  
        	if( b.size() > 0 )  
        	{  
        		std::cout << boost::beast::buffers(b.data()) << std::endl;  
        	}  
        }

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-07 02:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1152#issuecomment-395277335  

It looks like the other end is sending a WebSocket close frame, hence the error `beast::websocket::error::closed`.

---

## Comment 2

> Username: yongs2018  
> Created at: 2018-06-07 03:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1152#issuecomment-395279711  

but use libwebsocket request  wss://stream.binance.com:9443/ws/bnbbtc@depth is ok , or web page test is ok.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-07 05:21:32 UTC  
> Updated at: 2018-06-07 05:23:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1152#issuecomment-395296522  

When you use libwebsocket are you sending `"ws/bnbbtc@depth"` as a WebSocket message? Looking over the Binance APIs:  
  
https://github.com/binance-exchange/binance-official-api-docs/blob/master/web-socket-streams.md  
  
The documentation shows that streams are all accessed through a particular specification of the URI target in the request. There is nothing to indicate that the client is supposed to send a WebSocket message. Try removing this line from your code:  
  
```  
ws.write(boost::asio::buffer(std::string(text)));  
```

---

## Comment 4

> Username: yongs2018  
> Created at: 2018-06-07 05:53:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1152#issuecomment-395301425  

Thank you very much , you are a superman of boost.beast technology,  all perfect. !!!!

---

## Comment 5

> Username: yongs2018  
> Created at: 2018-06-07 05:53:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1152#issuecomment-395301503  

this issue Has been resolved. tks

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-06-07 11:51:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1152#issuecomment-395392900  

Great!
