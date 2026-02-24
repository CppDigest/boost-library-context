# #155 - co_return in cobalt::thread: cobalt::thread has no member return_value [Closed]

> Username: kilasuelika  
> Created at: 2024-01-29 10:02:08 UTC  
> Updated at: 2024-01-29 13:34:20 UTC  
> Closed at: 2024-01-29 13:34:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/155  

Following code:  
```  
#include <boost/cobalt.hpp>  
#include <boost/asio/steady_timer.hpp>  
using namespace boost;  
cobalt::thread my_thread()  
{  
    auto exec = co_await cobalt::this_coro::executor;  
    asio::steady_timer tim{ exec, std::chrono::milliseconds(50) };  
    co_await tim.async_wait(cobalt::use_op);  
    co_return 0;  
}  
  
int main(int argc, char* argv[])  
{  
    auto thr = my_thread();  
    thr.join();  
    return 0;  
}  
  
```  
is from official documentation: [cobalt::thread](https://www.boost.org/doc/libs/1_84_0/libs/cobalt/doc/html/index.html#thread).  
But it failed to compile in VS 2022 with `latest` standard:  
```  
1>D:\C++\test_cobalt\test_cobalt\test_cobalt.cpp(9,5): error C2039: 'return_value': is not a member of 'boost::cobalt::detail::thread_promise'  
```  
The error happens on the the line `co_return 0`.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-01-29 13:03:38 UTC  
> Url: https://github.com/boostorg/cobalt/issues/155#issuecomment-1914655105  

That would be a mistake in the docs. `cobalt::thread` should `co_return` `void`, not a value.

---

## Comment 2

> Username: kilasuelika  
> Created at: 2024-01-29 13:34:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/155#issuecomment-1914707347  

> That would be a mistake in the docs. `cobalt::thread` should `co_return` `void`, not a value.  
  
That's right. Thank you!
