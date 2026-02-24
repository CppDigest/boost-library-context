# #2394 - websocket::stream can't be reused once timed-out [Closed]

> Username: DavidKeller  
> Created at: 2022-03-09 10:40:03 UTC  
> Updated at: 2022-03-09 15:59:49 UTC  
> Closed at: 2022-03-09 15:59:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2394  

###  Problem  
  
When calling `async_handshake()` on a `websocket::stream` that previously timed-out, the `stream::impl_type::idle_counter` attribute is still equal to `1`, hence the `BOOST_ASSERT(idle_counter == 0);` from `stream::impl_type::update_timer()` aborts the whole program.  
  
```  
/home/david/.conan/data/boost/1.78.0/_/_/package/524ea35a8120baabdde02483add58d81bf541327/include/boost/beast/websocket/impl/stream_impl.hpp:417: void boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::impl_type::update_timer(const Executor&) [with Executor = boost::asio::any_io_executor; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true]: Assertion `idle_counter == 0' failed.  
Aborted (core dumped)  
```  
  
### Proposal  
  
Call `stream::impl_type::reset_idle()` from both `stream::impl_type::open()` and `stream::impl_type::reset()`.  
  
### Version of Beast  
  
**322** (Boost 1.78)  
  
### Steps necessary to reproduce the problem  
  
- Compile the code with `BOOST_ASSERT` enabled (e.g. Debug)  
- Connect a websocket and wait for timeout  
- Try to re-connect the very same websocket

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-03-09 11:17:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2394#issuecomment-1062815627  

This behaviour is expected.  
websocket streams are not reuseable after a failure. The stream must be destroyed and recreated.

---

## Comment 2

> Username: DavidKeller  
> Created at: 2022-03-09 15:45:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2394#issuecomment-1063059475  

Oh okay.  
  
Please ignore my merge request in that case.

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-03-09 15:59:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2394#issuecomment-1063082115  

Thanks for taking the trouble to raise this and provide a test. That kind of input is greatly appreciated.
