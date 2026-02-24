# #2700 - Deadlock during ssl_stream::async_handshake when turning off network with running zscaler client connector [Closed]

> Username: boasmeier  
> Created at: 2023-06-30 07:50:08 UTC  
> Updated at: 2023-07-03 08:49:54 UTC  
> Closed at: 2023-07-03 08:49:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2700  

During testing the behavior of some software components when network is turned off I experienced a deadlock during the ssl_stream::async_handshake of boost.beast. I did only experience this deadlock with running [Zscaler client connector](https://www.zscaler.com/platform/zscaler-client-connector) and did not test if this issue also occurs using other VPN's. Below a minimal compiling program, the log output, and a step-by-step guide on how to reproduce the deadlock. Let me know if you experience similar issues or if I am doing something wrong. Thanks in advance for your input and support!  
  
### Version of Beast and Asio  
  
boost.asio: 1.28.0  
boost.beast: 347  
  
### Steps necessary to reproduce the problem  
  
See [here](https://github.com/boasmeier/boost-beast-deadlock/blob/main/main.cpp) for a minimal compiling program and see [deadlock.log](https://github.com/boasmeier/boost-beast-deadlock/blob/main/deadlock.log) for the full program output with BOOST_ASIO_ENABLE_HANDLER_TRACKING enabled where the deadlock occured.  
  
The following steps reproduce the deadlock on my machine:  
  
1. Start Zscaler Client Connector.  
2. Start program with BOOST_ASIO_ENABLE_HANDLER_TRACKING=ON  
3. Turn Off network on OS level.  
4. Wait until program gets stuck and does not connect anymore.  
5. Mess around with Zscaler -> Turn off/on.  
6. Wait for about 1 minute.  
7. Turn-on network.  
8. At this point I would expect the program to connect again. However, it is stuck at `@asio|1688107293.822678|0|resolver@0x7fffb42679d8.cancel` and does not recover (at least not within 20min).  
  
  
### All relevant compiler information  
  
OS: Ubuntu 22.04.1  
GCC: 11.3.0  
CMake: 3.25.2  
Ninja: 1.10.2  
  
### Possible Workaround  
Use `boost::asio::use_future` for the async_connect and async_handshake calls and detach the io_context thread if the future does not return after e.g. 30s. However, this is no proper solution and does leak memory. It would be much better to tackle this deadlock on library level and prevent it from happening at all.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-30 12:00:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2700#issuecomment-1614552249  

This would be a boost.asio issue, not boost.beast. Since you call this is a dead-lock, where exactly does it hang? Can you step in with a debugger? Did you try per-op cancellation?

---

## Comment 2

> Username: boasmeier  
> Created at: 2023-07-03 08:49:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2700#issuecomment-1617653378  

I did not yet experiment with per-op cancellation but stepping in with a debugger shows that the io_context thread is stuck at the following line: https://github.com/chriskohlhoff/asio/blob/c465349fa5cd91a64bb369f5131ceacab2c0c1c3/asio/include/asio/detail/impl/epoll_reactor.ipp#L509. I opened an issue in asio repo including this additional information: https://github.com/chriskohlhoff/asio/issues/1325. Therefore I close this one.
