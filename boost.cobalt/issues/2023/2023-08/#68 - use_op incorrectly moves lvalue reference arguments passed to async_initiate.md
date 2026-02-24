# #68 - use_op incorrectly moves lvalue reference arguments passed to async_initiate [Closed]

> Username: anarthal  
> Created at: 2023-08-12 14:12:38 UTC  
> Updated at: 2023-09-05 01:00:48 UTC  
> Closed at: 2023-09-05 01:00:47 UTC  
> Url: https://github.com/boostorg/cobalt/issues/68  

This statement  
```  
// ws is a beast::websocket::stream, request is an http::request<http::string_body>  
co_await ws.async_accept(request, as_tuple(use_op));  
```  
  
Leaves `ws` in an invalid state by moving the websocket stream's implementation, which is a shared_ptr passed by lvalue reference to `asio::async_initiate`.  
  
The problem is in `op.hpp`, within these lines:  
  
```  
  template <typename Initiation, typename... InitArgs>  
  static auto initiate(Initiation && initiation,  
                       boost::async::use_op_t,  
                       InitArgs &&... args)  
      -> op_impl<std::decay_t<Initiation>, std::decay_t<InitArgs>...>  
  {  
    return op_impl<std::decay_t<Initiation>, std::decay_t<InitArgs>...>(  
        std::forward<Initiation>(initiation),  
        std::forward<InitArgs>(args)...);  
  }  
```  
  
This decay will end up moving lvalue references. Comparing this to other Asio's completion tokens, this should be:  
  
```  
  template <typename Initiation, typename... InitArgs>  
  static auto initiate(Initiation initiation, boost::async::use_op_t,  
                       InitArgs... args) -> op_impl<Initiation, InitArgs...> {  
    return op_impl<Initiation, InitArgs...>(std::move(initiation),  
                                            std::move(args)...);  
  }  
```  
  
With this change, the code works.
