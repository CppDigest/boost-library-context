# #209 - Closing the application keeps cobalt::thread running with a concurrent_channel [Closed]

> Username: matthijs  
> Created at: 2024-10-23 20:50:12 UTC  
> Updated at: 2024-10-30 08:38:33 UTC  
> Closed at: 2024-10-30 08:38:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/209  

Hi,  
  
I have a concurrent_channel which signals a thread when work is available. But when quitting the application (Ctrl-c) the thread will not finish.  
  
Here is a small reproducible example:  
```c++  
#include <iostream>  
  
#include <boost/asio/as_tuple.hpp>  
#include <boost/asio/experimental/concurrent_channel.hpp>  
#include <boost/asio/system_timer.hpp>  
#include <boost/cobalt/main.hpp>  
#include <boost/cobalt/promise.hpp>  
#include <boost/cobalt/thread.hpp>  
  
namespace cobalt = boost::cobalt;  
using cchannel = boost::asio::experimental::concurrent_channel<void(  
    boost::system::error_code, std::string)>;  
  
cobalt::promise<void> do_work(cchannel &chan) {  
  try {  
    while (chan.is_open()) {  
      auto [ec, payload] =  
          co_await chan.async_receive(boost::asio::as_tuple(cobalt::use_op));  
      if (ec) {  
        co_return;  
      }  
      std::cout << "got: " << payload << std::endl;  
    }  
  } catch (std::exception &e) {  
    std::cout << "exception: " << e.what() << std::endl;  
  }  
}  
  
cobalt::thread long_running(cchannel &chan) {  
  co_await do_work(chan);  
  co_return;  
}  
  
cobalt::promise<int> app() {  
  auto executor = co_await cobalt::this_coro::executor;  
  cchannel chan(executor);  
  auto thr = long_running(chan);  
  
  boost::asio::system_timer timer{executor};  
  timer.expires_after(std::chrono::hours{1});  
  while (!co_await cobalt::this_coro::cancelled) {  
    auto [ec] =  
        co_await timer.async_wait(boost::asio::as_tuple(cobalt::use_op));  
    if (ec) {  
      break;  
    }  
  }  
  
  // when done  
  std::cout << "closing channel\n";  
  chan.close();  
  std::cout << "joining thread\n";  
  thr.join();  
  std::cout << "yey done!\n"; // never gets here  
  co_return 0;  
}  
  
cobalt::main co_main(int argc, char **argv) {  
  co_return co_await app();  
}  
```  
  
I expected the application to quit correctly after closing the channel and joining the thread. But instead it never gets to the line 'yey done!'...  
  
My environment:  
Compiler: clang++-19  
C++ stdandard 20  
Using std libary from llvm

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-10-23 22:11:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/209#issuecomment-2433562693  

The `.join()` blocks the `executor`, so it cannot deliver the completion. You need to `co_await thr`, but since main is cancelled at that point you'll need to disable `throw_on_cancel` first or reset the cancellation state.  
  
```cpp  
  std::cout << "joining thread\n";  
  co_await cobalt::this_coro::reset_cancellation_state();  
  co_await thr;  
  std::cout << "yey done!\n"; // never gets here  
```

---

## Comment 2

> Username: matthijs  
> Created at: 2024-10-30 08:38:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/209#issuecomment-2446192062  

Ah thanks! That solves my issue.
