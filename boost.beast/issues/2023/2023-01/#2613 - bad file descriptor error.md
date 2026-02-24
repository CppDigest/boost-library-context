# #2613 - bad file descriptor error [Closed]

> Username: flashnuke  
> Created at: 2023-01-04 15:58:26 UTC  
> Updated at: 2023-01-05 07:54:00 UTC  
> Closed at: 2023-01-05 07:54:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2613  

Hello  
I'm trying to set up a websocket stream with nagle's algorithm disabled  
  
This is what I'm attempting to do:  
```c++  
beast::get_lowest_layer(*ws_).socket().set_option(boost::asio::ip::tcp::no_delay(true));  
```  
where `ws_` is of type `std::shared_ptr<beast::websocket::stream<beast::tcp_stream>>`  
  
This is the error I'm getting:  
  
> error on websocket async manager: set_option: Bad file descriptor [system:9 at /usr/local/include/boost/asio/detail/reactive_socket_service.hpp:174:33 in function 'set_option']  
  
thanks in advance

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-04 22:43:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2613#issuecomment-1371512585  

Is `ws_` holding a valid socket when you do that?

---

## Comment 2

> Username: flashnuke  
> Created at: 2023-01-05 07:54:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2613#issuecomment-1371886865  

> Is `ws_` holding a valid socket when you do that?  
  
your question made me realize I was setting this option too early. I moved it to the callback of `async_connect` and it works great now  
  
thanks!
