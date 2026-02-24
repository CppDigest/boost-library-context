# #2836 - websocket reconnect [Closed]

> Username: ahao1995  
> Created at: 2024-03-14 02:14:12 UTC  
> Updated at: 2024-05-06 08:03:55 UTC  
> Closed at: 2024-05-06 08:03:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2836  

I want to do reconnect, if read error, should I call async close and then call async_connect. or there is better ways?  
I find if the error is connection by peer, call async close not call async_close callback, it is right?  
```  
using websocket_stream = std::optional<boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>>;  
websocket_stream ws_stream_;  
ws_stream_->async_read(buffer_, [this](boost::system::error_code ec,  
                                         std::size_t n) {  
    if (!ec) {  
      (read_cb_)(buf, ec);  
    }  
    else {  
    //should call async close or async connect  
      async_close();  
    }  
  });  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-14 05:34:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2836#issuecomment-1996562055  

`beast::websocket::stream` is not reusable; you have to construct a new one and go through the process of connection and handshake again. this is just like when you want to create a new websocket stream.

---

## Comment 2

> Username: ahao1995  
> Created at: 2024-03-15 08:51:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2836#issuecomment-1999193746  

> `beast::websocket::stream` is not reusable; you have to construct a new one and go through the process of connection and handshake again. this is just like when you want to create a new websocket stream.  
  
if read error, shold I call async close before? or just construct a new stream to do reconnect?

---

## Comment 3

> Username: ashtum  
> Created at: 2024-03-15 08:59:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2836#issuecomment-1999208309  

If `read` completes with an error, it indicates there is an issue with the connection, so `async_close` would also complete with an error. Therefore, there is no need to call `async_close`; the destructor of `websocket::stream` will forcefully close the underlying socket.
