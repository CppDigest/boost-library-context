# #2018 - skip websocket handshake and begin framing data once the underlying connection is established [Closed]

> Username: niclar  
> Created at: 2020-07-15 16:40:46 UTC  
> Updated at: 2020-07-15 19:42:00 UTC  
> Closed at: 2020-07-15 19:42:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2018  

Hi, how would I skip the websocket handshake in beast and begin framing data once the underlying connection is established ?  
  
-As I suspect that what my third party server endpoint wants me to do if I'm not a browser, since   
it insists of handling the upgrade request as somethin else..   
  
  
Thanks  
Niclas

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-15 16:50:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2018#issuecomment-658877945  

Beast only supports compliant websocket behavior, you cannot skip the handshake.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-07-15 17:02:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2018#issuecomment-658885169  

> As I suspect that what my third party server endpoint wants me to do if I'm not a browser, since  
it insists of handling the upgrade request as somethin else  
  
I suspect what you're seeing is something else.  
  
Can you share any background information?

---

## Comment 3

> Username: niclar  
> Created at: 2020-07-15 18:24:11 UTC  
> Updated at: 2020-07-15 18:44:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2018#issuecomment-658927536  

@madmongo1 yeah that might be the case..    
  
-I'm connecting to mwstest.infrontservices.com. The ssl handshake is successful (no certificates/validation setup though).  
In wireshark it seems like, from what I can tell, that the websocket handshake isn't done over ssl for some reason.   
-Is the handover from ssl to the websocket layer faulting for some reason ?   
The server responds with the blanket http page that it can't be reached via http. All works fine via chrome. And I don't think its the headers in general, i've tried a myriad (some commented below).  
   
  
```  
---------- code gist:  
   typedef boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>> WebSocket;  
     
   boost::asio::io_context m_ioContext(1);  
   boost::asio::ssl::context m_sslContext(m_ioContext);  
   boost::asio::ip::tcp::resolver m_resolver(boost::asio::ssl::context::tlsv12_client);  
   WebSocket m_webSocket(m_ioContext, m_sslContext);  
  
...   Connect(const std::string& hostname, uint16_t port)  
{  
	auto results = m_resolver.resolve(hostname, std::to_string(port));  
  
	auto& tcp_stream = boost::beast::get_lowest_layer(m_webSocket);  
	auto ep = tcp_stream.connect(results);  
        auto host = create_string(hostname << ':' << ep.port());  
  
	tcp_stream.expires_after(std::chrono::seconds(30));  
  
	auto& ssl_stream = m_webSocket.next_layer();  
	ssl_stream.set_verify_mode(boost::asio::ssl::verify_none);  
	ssl_stream.handshake(boost::asio::ssl::stream_base::client);  
  
	tcp_stream.expires_never();  
  
	 m_webSocket.set_option(boost::beast::websocket::stream_base::timeout::suggested(boost::beast::role_type::client));  
  
	 m_webSocket.set_option(boost::beast::websocket::stream_base::decorator(  
                [](boost::beast::websocket::request_type& req)  
		{  
			req.set(boost::beast::http::field::user_agent, "X");  
			//req.set("Connection", "Upgrade");  
			//req.set("Host", "mwstest.infrontservices.com");  
			//req.set(boost::beast::http::field::content_type, "text/plain");  
			//req.set("Access-Control-Allow-Origin", "*");  
			//req.set(boost::beast::http::field::transfer_encoding, "chunked");  
			//req.set("Origin", "https://www.websocket.org");  
			//req.set(boost::beast::http::field::version, "1.1");  
/* Edge working HEADERS:  
			//GET wss://mwstest.infrontservices.com/?encoding=text HTTP/1.1  
			req.set("Host", "mwstest.infrontservices.com");  
			req.set("Connection", "Upgrade");  
			req.set("Pragma", "no-cache");  
			req.set("Cache-Control", "no-cache");  
			req.set("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.116 Safari/537.36 Edg/83.0.478.61");  
			req.set("Upgrade", "websocket");  
			req.set("Origin", "https://www.websocket.org");  
			req.set("Sec-WebSocket-Version", "13");  
			req.set("Accept-Encoding", "gzip, deflate, br");  
			req.set("Accept-Language", "en-US,en;q=0.9");  
			req.set("Sec-WebSocket-Key", "1eSCv+cGdOQOZfU03Zfcpg==");  
			req.set("Sec-WebSocket-Extensions", "permessage-deflate; client_max_window_bits");  
*/  
		}));  
  
	//https://tools.ietf.org/html/rfc6455#section-4.2.2  
	boost::beast::websocket::response_type res;  
	m_webSocket.handshake(res, host, "/");                                 <---------  BAILS  
	  
	assert(IsConnected());	  
	}  
  
```

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-07-15 18:33:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2018#issuecomment-658932551  

A common cause of SSL problems is when the server is behind a proxy which relies on the SNI hostname being set before the TLS handshake:  
  
```  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if(! SSL_set_tlsext_host_name(stream.native_handle(), host))  
        {  
            beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
            throw beast::system_error{ec};  
        }  
```

---

## Comment 5

> Username: niclar  
> Created at: 2020-07-15 18:38:50 UTC  
> Updated at: 2020-07-15 18:40:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2018#issuecomment-658935108  

Yeah I read that "bug" report and actually tried it but it made no difference unfortunately  
  
And wouldn't the ssl handshake bail if that was the issue ?

---

## Comment 6

> Username: niclar  
> Created at: 2020-07-15 18:55:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2018#issuecomment-658943372  

My bad, it actually worked. I did the SNI setting after the ssl handshake, reading up on SNI and doing it before remedied the issue.  
  
Thank you!
