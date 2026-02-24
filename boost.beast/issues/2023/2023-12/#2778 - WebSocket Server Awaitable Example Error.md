# #2778 - WebSocket Server Awaitable Example Error [Closed]

> Username: Anefu  
> Created at: 2023-12-23 07:34:13 UTC  
> Updated at: 2023-12-24 06:18:52 UTC  
> Closed at: 2023-12-24 06:18:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2778  

### Version of Beast  
  
Beast Version: 351  
  
### Steps necessary to reproduce the problem  
  
- Compile the [client](https://github.com/boostorg/beast/blob/master/example/websocket/client/awaitable/websocket_client_awaitable.cpp) and [server](https://github.com/boostorg/beast/blob/master/example/websocket/server/awaitable/websocket_server_awaitable.cpp) examples.  
- Run the server on any port, with 1 thread. Run the client on the same port as the server.  
- Notice: The client code executes as expected but the server returns an error  
```bash  
Error in session: Operation canceled [system:125 at /usr/include/boost/beast/websocket/impl/stream_impl.hpp:355:13 in function 'bool boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::impl_type::check_stop_now(boost::beast::error_code&)']  
```  
  
### All relevant compiler information  
  
Compiler: GCC 11.4.0  
Boost Version: 1.84.0  
  
The more information you provide the sooner your issue  
can get resolved!
