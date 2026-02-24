# #171 - boost::asio customization [Closed]

> Username: dimarusyy  
> Created at: 2018-11-26 07:56:40 UTC  
> Updated at: 2020-12-30 00:57:40 UTC  
> Closed at: 2020-12-30 00:57:39 UTC  
> Url: https://github.com/boostorg/asio/issues/171  

Hello. I would appreciate if somebody who supports boost::asio would pay attention to this post. In fact, I need to establish tcp FASTOPEN connection on client side with boost::asio.   
Unfortunately I failed to extend basic_stream_socket / basic_socket classes because of private access to get_implementation() method and inability to access send_to/async_send_to of implementation sublayer (reactive_socket_service or win_iocp_socket_service). That made me to copy all tcp-related classes starting from stream_socket_service and up to access to 2 methods send_to()/async_send_to() from posix/win32 layers that I need.   
  
As an option, basic_io_object::get_implementation() could be declared as public or have overloaded constructor like basic_io_object(boost::asio::io_service& io_service, IoObjectService::implementation_type impl_service) to allow injecting implementation.  
  
Also, in boost 1.66 the BOOST_ASIO_SVC_ACCESS define was introduced and changed public->protected base class access for basic_socket. That significantly limits customization of top-level asio classes.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:57:38 UTC  
> Url: https://github.com/boostorg/asio/issues/171#issuecomment-752290702  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#656](https://github.com/chriskohlhoff/asio/issues/656).
