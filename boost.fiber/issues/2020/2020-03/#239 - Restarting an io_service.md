# #239 - [boost asio interaction] Restarting an io_service [Open]

> Username: niekbouman  
> Created at: 2020-03-04 14:09:33 UTC  
> Updated at: 2020-03-07 06:53:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/239  

Hi,  
  
I am using the Boost.Fiber <-> Boost.Asio coupling via the `boost::fibers::asio::round_robin` scheduler, and I am trying to relaunch an asio `io_service`, that has previously been stopped.  
  
The problem is, that the scheduler (or fiber) hangs after the second execution of the `.run()` method.  
In the code example below, the "this line is never reached\n" string is never printed.  
  
```cpp  
#include <iostream>  
#include <boost/asio/io_context.hpp>  
#include "round_robin.hpp"  
#include <boost/fiber/all.hpp>  
  
using io_context_ptr = std::shared_ptr<boost::asio::io_service>;  
  
void start(io_context_ptr io_ctx)  
{  
  
  boost::asio::system_timer t(*io_ctx);  
  t.expires_after(std::chrono::seconds(1));  
  t.async_wait(boost::fibers::asio::yield);  
  
  io_ctx->stop();  
  
}  
  
int main()  
{  
    io_context_ptr io_ctx = std::make_shared<boost::asio::io_service>();  
    boost::fibers::use_scheduling_algorithm<boost::fibers::asio::round_robin>(io_ctx);  
    auto f = boost::fibers::fiber(start, io_ctx);  
    io_ctx->run();  
    f.join();  
  
    std::cout << "foo\n";  
  
    io_ctx->restart();  
    auto f2 = boost::fibers::fiber(start, io_ctx);  
  
    std::cout << "bar\n";  
      
    io_ctx->run();  
    std::cout << "this line is never reached\n";  
    f2.join();  
  
}  
```

---

## Comment 1

> Username: jpcordovae  
> Created at: 2020-03-07 06:53:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/239#issuecomment-596054626  

if I remember well you have to reset your service before restart it (there is a restart function btw). I suggest to carefully read the documentation, sometimes functions names are misinterpreted or require a context not so evident.   
  
https://stackoverflow.com/questions/15568100/confused-when-boostasioio-service-run-method-blocks-unblocks
