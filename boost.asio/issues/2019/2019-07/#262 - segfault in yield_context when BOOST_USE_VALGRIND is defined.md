# #262 - segfault in yield_context when BOOST_USE_VALGRIND is defined [Closed]

> Username: cspanier  
> Created at: 2019-07-19 07:30:32 UTC  
> Updated at: 2020-12-30 01:07:05 UTC  
> Closed at: 2020-12-30 01:07:03 UTC  
> Url: https://github.com/boostorg/asio/issues/262  

The following code crashes on Linux when `BOOST_USE_VALGRIND` is defined:  
  
```  
#include <boost/asio.hpp>  
#include <boost/asio/spawn.hpp>  
  
int main(int /*argc*/, char* /*argv*/[])  
{  
  boost::asio::io_context context;  
  boost::asio::high_resolution_timer timer(context, std::chrono::seconds(1));  
  boost::asio::spawn(context, [&](boost::asio::yield_context yield) {  
    // The following line crashes in jump_fcontext while yielding the coro, but  
    // only if BOOST_USE_VALGRIND is defined:  
    timer.async_wait(yield);  
  });  
  context.run();  
  return 0;  
}  
```  
  
`g++ -DBOOST_USE_VALGRIND main.cpp -lboost_coroutine -lboost_context -lpthread -o test && ./test`  
  
The issue happens both with GCC 9.1.0 and Clang 8.0.0 on x86_64 (tested on Arch and Debian), and on aarch64 (RaspberryPi 3) running Arch in 64bit mode.  
  
I know that spawn.hpp still makes use of deprecated Boost::Coroutines V1.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:07:02 UTC  
> Url: https://github.com/boostorg/asio/issues/262#issuecomment-752292553  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#705](https://github.com/chriskohlhoff/asio/issues/705).
