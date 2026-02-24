# #533 - ASSERT in websocket stream [Closed]

> Username: ksergey  
> Created at: 2017-06-23 19:38:57 UTC  
> Updated at: 2017-06-25 20:25:40 UTC  
> Closed at: 2017-06-25 20:24:53 UTC  
> Url: https://github.com/boostorg/beast/issues/533  

Got assert inside Beast:  
```  
/home/ksergey/dev/bitfinex_feed_handler/deps/Beast/include/beast/websocket/impl/close.ipp:241: void beast::websocket::stream<NextLayer>::close(const beast::websocket::close_reason&, beast::error_code&) [with NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; beast::error_code = boost::system::error_code]: Assertion `! wr_close_' failed.  
Aborted  
```  
  
I try to close socket while async_read active. Why it happens? How to cancel all asynchronous operations?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-23 20:01:31 UTC  
> Url: https://github.com/boostorg/beast/issues/533#issuecomment-310759773  

This means you are trying to call `async_close` when you already have a call to `async_write` active. If you want to cancel all async I/O, call `next_layer().cancel()`. Make sure you do this from the strand.

---

## Comment 2

> Username: ksergey  
> Created at: 2017-06-24 15:26:27 UTC  
> Updated at: 2017-06-24 15:26:40 UTC  
> Url: https://github.com/boostorg/beast/issues/533#issuecomment-310845073  

I call `close` while `async_read` active. Is it the same case?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-24 15:56:15 UTC  
> Url: https://github.com/boostorg/beast/issues/533#issuecomment-310847006  

>I call close while async_read active. Is it the same case?  
  
No. The assert can also happen if you call `close` twice.

---

## Comment 4

> Username: ksergey  
> Created at: 2017-06-25 18:31:02 UTC  
> Url: https://github.com/boostorg/beast/issues/533#issuecomment-310919479  

Actually I call `close` only one time (while `async_read` active). My current workaround for close active socket is  
```                                                                                         
    __force_inline void disconnect()                                                                                          
    {                                                                                                                         
        boost::system::error_code error;                                                                                      
                                                                                                                              
        /* Force close TCP socket */                                                                                          
        stream_.lowest_layer().close(error);                                                                                  
        if (__unlikely(error)) {                                                                                              
            log_error("Close error [ {} ]", error.message());                                                                 
        }                                                                                                                     
                                                                                                                              
        /* Cancel timer if active */                                                                                          
        timer_.cancel_one();                                                                                                  
    }   
```  
  
Could you please provide a right way to force close an active socket?  
  
Also how to cancel `async_handshake`?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-25 18:33:25 UTC  
> Updated at: 2017-06-25 18:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/533#issuecomment-310919605  

To cancel all asynchronous operations use `stream_.lowest_layer().cancel(ec)`  
  
Are you calling `disconnect` from the strand?  
  
Are you calling `websocket::stream::close` from the strand?

---

## Comment 6

> Username: ksergey  
> Created at: 2017-06-25 20:24:53 UTC  
> Url: https://github.com/boostorg/beast/issues/533#issuecomment-310926014  

@vinniefalco thanks!  
  
I found the issue: I try call `close` inside `async_read` callback and then try again `async_read` on invalid socket.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-25 20:25:40 UTC  
> Url: https://github.com/boostorg/beast/issues/533#issuecomment-310926052  

Glad to hear it! Make sure you are on the strand when you access the socket, or else undefined behavior!
