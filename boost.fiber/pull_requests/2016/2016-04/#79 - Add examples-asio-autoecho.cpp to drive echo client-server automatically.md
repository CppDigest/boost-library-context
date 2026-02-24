# #79 Add examples/asio/autoecho.cpp to drive echo client/server automatically. [Merged]

> Username: nat-goodspeed  
> Created at: 2016-04-06 01:27:00 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2016-04-06 06:04:25 UTC  
> Closed at: 2016-04-06 06:04:25 UTC  
> Url: https://github.com/boostorg/fiber/pull/79  

This example program is intended to exercise both ends of several client / server connections on multiple threads, and terminate gracefully when done. It illustrates:  
- multiple threads calling run() on the same asio::io_service instance  
- shutdown by calling io_service::stop() when the last client finishes  
- interrupting the listening server fiber.  
  
Warning: we may need to interrupt the server fiber _before_ calling io_service::stop().

---
