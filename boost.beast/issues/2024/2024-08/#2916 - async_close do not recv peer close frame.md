# #2916 - async_close do not recv peer close frame [Closed]

> Username: ahao1995  
> Created at: 2024-08-08 09:03:40 UTC  
> Updated at: 2024-10-16 07:19:35 UTC  
> Closed at: 2024-10-16 07:19:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2916  

I use `async_close` to close the socket, but after 5min recv callback, have any method to force close the connection, and not wait the close frame  
may be I should call  
```  
std::optional<boost::beast::websocket::stream<  
    boost::beast::ssl_stream<boost::beast::tcp_stream>>> ws_stream_;  
//force close?  
boost::beast::get_lowest_layer(*ws_stream_).cancel();  
```  
after that I want to do reconnect  
```  
ws_stream_.emplace(exec_, ctx_);  
```  
it is right?  
thanks

---

## Comment 1

> Username: ashtum  
> Created at: 2024-08-08 09:10:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2916#issuecomment-2275331746  

Have you set a timeout on the stream? https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/using_websocket/timeouts.html

---

## Comment 2

> Username: ahao1995  
> Created at: 2024-08-08 09:39:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2916#issuecomment-2275392322  

> Have you set a timeout on the stream? https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/using_websocket/timeouts.html  
  
I do not use timeout, I want to close it quickly and reconnect,  just like force close not graceful, is there some method?

---

## Comment 3

> Username: ashtum  
> Created at: 2024-08-08 09:52:05 UTC  
> Updated at: 2024-08-08 10:26:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2916#issuecomment-2275416136  

`beast::get_lowest_layer(*ws_stream_).close();` will do it, or you can simply ignore the stream object and let it be destroyed.

---

## Comment 4

> Username: ahao1995  
> Created at: 2024-08-08 13:56:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2916#issuecomment-2275899405  

> `beast::get_lowest_layer(*ws_stream_).close();` will do it, or you can simply ignore the stream object and let it be destroyed.  
  
you mean direct use `ws_stream_.emplace(exec_, ctx_); ` let it destroy?

---

## Comment 5

> Username: ashtum  
> Created at: 2024-08-08 14:43:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2916#issuecomment-2276007932  

Yes, that would have the same effect as closing the underlying socket.
