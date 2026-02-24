# #1733 - websocket async_accept invalid [Closed]

> Username: qtcdxxyuc  
> Created at: 2019-10-18 02:42:08 UTC  
> Updated at: 2019-10-21 02:49:39 UTC  
> Closed at: 2019-10-21 02:49:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1733  

### Version of Beast  
  
266  
  
### Steps necessary to reproduce the problem  
  
`websocket.async_accept()` invalid.  
  
```c++  
websocket.accept(); //it will be return  
-----------------------  
websocket.async_accept(use_future).get();   
//not return,but client show handshake  successed.  
//even replacing other async_handler,still no return;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-18 02:42:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-543461347  

No idea what you mean

---

## Comment 2

> Username: qtcdxxyuc  
> Created at: 2019-10-18 02:58:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-543466414  

```c++  
using namespace boost::asio;  
 using namespace boost::beast;  
 io_context ioc(1);  
 ip::tcp::acceptor ac(ioc,{ip::make_address_v4("127.0.0.1"),port});  
  
 auto socket = ac.accept();  
 websocket::stream<tcp_stream> ws(std::move(socket));  
 ws.accept();   
//Can be executed here  
  
```  
  
If we replace`ws.accept();`to`ws.async_accept(use_future).get();`  
  
this will not continue.  
  
async_accept() not set promise, so we block here.  
  
Even if  used  yield; e.g.  
  
```c++  
asio::spawn([ws(move(ws))](yield_context yield){  
    boost::system::error_code ec;  
	websocket::stream<tcp_stream> ws(std::move(socket));  
	async_accept(yield[ec]);      
    //can not run to here  
});  
```

---

## Comment 3

> Username: qtcdxxyuc  
> Created at: 2019-10-18 06:13:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-543533192  

it is block with `boost/beast/accept.hpp:218`:  
  
```c++  
// Send response  
BOOST_ASIO_CORO_YIELD  
http::async_write(  
    impl.stream(), res_, std::move(*this));  
//can not go to here  
if(impl.check_stop_now(ec))  
    goto upcall;  
//...  
```

---

## Comment 4

> Username: qtcdxxyuc  
> Created at: 2019-10-18 06:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-543543309  

when i  try to write a own response with ws_stream,  
  
```c++  
http::response<http::empty_body> response;  
http::async_write(ws_stream->next_layer(),response,boost::asio::use_future).get();  
//can not go to here  
```  
  
server can not contine,block with async_write.  
  
but client get own response.  
  
it is seem async_write happed but not notify the handler.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-10-18 08:04:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-543584717  

Where is the call to `ioc.run()`?

---

## Comment 6

> Username: qtcdxxyuc  
> Created at: 2019-10-18 08:06:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-543585731  

other std::thread().

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-10-18 08:08:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-543586800  

Does this example work for you:  https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi

---

## Comment 8

> Username: qtcdxxyuc  
> Created at: 2019-10-21 02:49:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1733#issuecomment-544328416  

It seems that I have no suitable blocking called io_context
