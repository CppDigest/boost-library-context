# #2690 - PING command problems when network disconnect occures [Closed]

> Username: marincovic  
> Created at: 2023-05-18 17:01:58 UTC  
> Updated at: 2023-10-28 16:34:05 UTC  
> Closed at: 2023-10-28 16:34:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2690  

### Version of Beast  
  
1.80.0  
  
### All relevant compiler information  
  
Hello,  
  
I have a small problem of understanding how the ping command works.   
I am using the following code  
```  
	boost::beast::error_code ec;  
	m_wss->ping(beast::websocket::ping_data(), ec);  
	if (ec) {  
		logger->Log(INFO, "not available.");  
		return false;  
	}  
	else {  
		logger->Log(INFO, std::string("available." + this->GetDateTime()).c_str());  
		return true;  
	}  
```  
  
This is done in a loop until a shutdown signal is given. During the execution I physically disconnect the cable from the laptop but the ping still says that the connection is available. This goes on until a async_read run in a different function receives an error.  
My question is how is the ping command able to write to the network when there is no connection and why does it not throw an error because no PONG was received back?

---

## Comment 1

> Username: ashtum  
> Created at: 2023-08-18 17:21:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2690#issuecomment-1684214043  

@marincovic It seems you are using a mix of sync and async interfaces which can easily lead to undefined behavior, further on you need to check for pong packet to make sure connection is alive.   
please note if you are using [websocket::stream](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html) it will automatically handles pinging operation as long as you are waiting on [async_read](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html).  
  
> Pong frames and close frames sent by the implementation while the read operation is outstanding do not prevent the application from also writing message data, sending pings, sending pongs, or sending close frames.  
  
  
you can set appropriate timeouts using [websocket::stream::set_option](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option.html)  and [websocket::stream_base::timeout](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream_base__timeout.html) option.
