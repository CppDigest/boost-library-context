# #2307 - Boost::beast::ssl_stream unable to deference the shared pointer, need to transform ssl_stream to websocket::stream [Closed]

> Username: mhassanshafiq  
> Created at: 2021-09-03 00:01:12 UTC  
> Updated at: 2021-09-03 01:17:25 UTC  
> Closed at: 2021-09-03 01:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2307  

I am trying to write a https flex server, that can upgrade to websocket based on upgrade request. I used this example as a guide, but this is using member objects and not shared_ptrs https://www.boost.org/doc/libs/1_73_0/libs/beast/example/http/server/flex/http_server_flex.cpp  
  
https class does the ssl handshake on `std::shared_ptr<boost::beast::ssl_stream<boost::beast::tcp_stream>> m_ptls_stream`, now I dont want to do the ssl handshake again when I transform it to websocket as I believe it terminates the connection.  
  
Now I need to transfer this stream to websocket class and transform it into type  
  
```  
std::shared_ptr<boost::beast::websocket::stream<  
        boost::beast::ssl_stream<boost::beast::tcp_stream>>>  
```  
But for some reason the constructor of websocket stream doesn't accept a shared pointer, and I am unable to dereference the ssl_stream shared_ptr as I get the error that the copy constructor is deleted  
  
> Severity Code Description Project File Line Suppression State Error C2280 'boost::beast::ssl_streamboost::beast::tcp_stream::ssl_stream(const boost::beast::ssl_streamboost::beast::tcp_stream &)': attempting to reference a deleted function D:\Work\remote_pc\out\build\x64-Debug\remote_pc D:\Work\boost_1_73_0\boost\asio\impl\executor.hpp 218  
  
```  
void async_ws_client::add_stream(std::shared_ptr<boost::beast::ssl_stream<boost::beast::tcp_stream>>&& ptls_stream)  
{     
    if (m_ptls_context)  
    {         
        m_p_wss_stream = std::make_shared<  
            boost::beast::websocket::stream<  
            boost::beast::ssl_stream<  
            boost::beast::tcp_stream>>>(std::move(*ptls_stream), *m_ptls_context);  
    }  
}  
```  
Feels like im missing something, unable to figure it out for a couple of days. Please help..!!  
  
Also, if I do it this way  
```  
m_p_wss_stream = std::make_shared<  
				boost::beast::websocket::stream<  
				boost::beast::ssl_stream<  
				boost::beast::tcp_stream>>>(std::move(ptls_stream->next_layer()),  
					*m_ptls_context);  
```  
The socket throws `error : uninitialized `when I do async_accept() on the stream after creating it.

---

## Comment 1

> Username: mhassanshafiq  
> Created at: 2021-09-03 01:17:05 UTC  
> Updated at: 2021-09-03 01:17:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2307#issuecomment-912177139  

made a stupid mistake in understanding, cannot pass tls_context into websocket::stream constructor. The following code worked  
```  
m_p_wss_stream = std::make_shared<  
            boost::beast::websocket::stream<  
            boost::beast::ssl_stream<  
            boost::beast::tcp_stream>>>(std::move(*ptls_stream));  
```
