# #180 - not clear how to use custom tracking handler [Closed]

> Username: jdmairs  
> Created at: 2018-12-22 19:26:04 UTC  
> Updated at: 2020-12-30 00:58:16 UTC  
> Closed at: 2020-12-30 00:58:15 UTC  
> Url: https://github.com/boostorg/asio/issues/180  

I get compiler errors when I try to enable the custom tracking handler as described here:  
Custom Tracking  
Handling tracking may be customised by defining the BOOST_ASIO_CUSTOM_HANDLER_TRACKING macro to the name of a header file (enclosed in "" or <>). This header file must implement the following preprocessor macros:  
  
#include <boost/asio.hpp>  
#define BOOST_ASIO_ENABLE_HANDLER_TRACKING 1  
#define BOOST_ASIO_CUSTOM_HANDLER_TRACKING "customTrackingHandler.hpp"  
  
where I copy/pasted into the filename "customTrackingHandler.hpp" the example from [https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/examples/cpp11_examples.html#boost_asio.examples.cpp11_examples.handler_tracking]  
  
The reason I am trying this is that 2-3x a month our integration test fails because the io_service.run() does not drop out of the blocking call.  We typically load up a bunch of timers and sockets and then on shutdown cancel them all individually.  For whatever reason we sometimes stay blocked.  I'm hoping this will give me insight.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:58:14 UTC  
> Url: https://github.com/boostorg/asio/issues/180#issuecomment-752290839  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#660](https://github.com/chriskohlhoff/asio/issues/660).
