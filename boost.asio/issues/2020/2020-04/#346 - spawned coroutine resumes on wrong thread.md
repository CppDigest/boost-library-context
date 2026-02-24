# #346 - spawned coroutine resumes on wrong thread. [Closed]

> Username: wvandamme  
> Created at: 2020-04-24 14:44:57 UTC  
> Updated at: 2020-12-30 01:14:16 UTC  
> Closed at: 2020-12-30 01:14:15 UTC  
> Url: https://github.com/boostorg/asio/issues/346  

When asio is used with multiple threads (e.g io_context.run() in multiple threads or a non-asio worker thread), then the coroutine get resumes on a thread which is different from the one assigned to the strand, which of course makes things all go bogus.  
  
Below you can find a reproducer, that triggers the problem on windows 10,  MSCV 2019 (16.4.6)  
Microsoft (R) C/C++ Optimizing Compiler Version 19.24.28319 for x64  
Boost version 1.72.0.  
  
  
----------  
```  
#include <boost/asio.hpp>  
#include <boost/asio/spawn.hpp>  
  
#include <thread>  
#include <iostream>  
  
template <typename Token>  
auto  
something_async(boost::asio::io_context& c_, Token&& token_)  
{  
  using result_type = typename boost::asio::async_result<std::decay<Token>::type, void (boost::system::error_code)>;  
  typename result_type::completion_handler_type handler(std::forward<Token>(token_));  
  result_type result(handler);  
  auto work = boost::asio::make_work_guard(c_);  
  std::thread t([&c_, work, handler] ()  
  {  
    boost::asio::asio_handler_invoke(handler, boost::system::error_code());  
  });  
  t.detach();  
  return result.get();  
}  
  
void func1(boost::asio::io_context::strand& c_, boost::asio::yield_context yield_)  
{  
  for (;;)  
  {  
    assert(c_.running_in_this_thread());  
    something_async(c_.context(), yield_);  
    assert(c_.running_in_this_thread()); // This assert is triggered unexpectedly.  
    std::cout << "1##################################" << std::endl;  
    std::this_thread::sleep_for(std::chrono::seconds(1));  
    std::cout << "2##################################" << std::endl;  
  }  
}  
  
void func2(boost::asio::io_context::strand& c_, boost::asio::yield_context yield_)  
{  
  for (;;)  
  {  
    assert(c_.running_in_this_thread());  
    something_async(c_.context(), yield_);  
    assert(c_.running_in_this_thread()); // This assert is triggered unexpectedly.  
    std::cout << "1@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@" << std::endl;  
    std::this_thread::sleep_for(std::chrono::seconds(1));  
    std::cout << "2@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@" << std::endl;  
  }  
}  
  
int main(int argc_, char* argv_[])  
{  
  boost::asio::io_context c;  
  boost::asio::io_context::strand s(c);  
  boost::asio::spawn(s, std::bind(func1, std::ref(s), std::placeholders::_1));  
  boost::asio::spawn(s, std::bind(func2, std::ref(s), std::placeholders::_1));  
  unsigned int const pool_size = 24;  
  boost::asio::thread_pool pool(pool_size);  
  
  for (unsigned int i = 0; i < pool_size; ++i)  
  {  
    boost::asio::post(pool, [&c] ()  
    {  
      c.run();  
    });  
  }  
  
  pool.join();  
  return 0;  
}  
```

---

## Comment 1

> Username: wvandamme  
> Created at: 2020-04-29 10:45:34 UTC  
> Url: https://github.com/boostorg/asio/issues/346#issuecomment-621122789  

I spend some time debugging and trying to understand the boost.asio implementation. (most of the stuff is still a dark magic, but anyway). And this is what I have found:  
```  
template <typename Token>  
auto  
something_async(boost::asio::io_context& c_, Token&& token_)  
{  
  using result_type = typename boost::asio::async_result<std::decay<Token>::type, void (boost::system::error_code)>;  
  typename result_type::completion_handler_type handler(std::forward<Token>(token_));  
  result_type result(handler);  
  auto executor = boost::asio::get_associated_executor(handler);  
  auto work = boost::asio::make_work_guard(c_);  
  std::thread t([&c_, work, executor, handler] ()  
  {  
  
    boost::asio::post(c_,boost::asio::bind_executor(executor, std::bind(handler, boost::system::error_code())));  
    // or -  
    // boost::asio::post(executor, std::bind(handler, boost::system::error_code()));  
  });  
  t.detach();  
  return result.get();  
}  
```  
So, when I replace the asio_handler_invoke() with a post call onto the handler's associated executor, then the testcase works fine and the asserts are not triggered.  
  
**So, was the first implementation of something_async() wrong?** and therefore the programmer is responsible to post the completion handler on its associated executor.  (I actually assumed that is exactly what asio_handler_invoke() was suppose to do).  
  
**Or is there something I don't get?** And I'm just lucky in the second/working implementation of something_async().  
  
Comments or advice. Anybody?

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:14:14 UTC  
> Url: https://github.com/boostorg/asio/issues/346#issuecomment-752293642  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#760](https://github.com/chriskohlhoff/asio/issues/760).
