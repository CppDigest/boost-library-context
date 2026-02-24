# #50 - detached example doesn't seem to work [Closed]

> Username: anarthal  
> Created at: 2023-07-24 12:29:04 UTC  
> Updated at: 2023-07-25 11:02:07 UTC  
> Closed at: 2023-07-25 11:02:07 UTC  
> Url: https://github.com/boostorg/cobalt/issues/50  

```  
#include <boost/asio/steady_timer.hpp>  
#include <boost/async/main.hpp>  
  
#include <cstdio>  
  
#include "boost/async/detached.hpp"  
#include "boost/async/op.hpp"  
  
namespace async = boost::async;  
namespace asio = boost::asio;  
  
async::detached delayed_print(std::chrono::milliseconds ms)  
{  
    asio::steady_timer tim{co_await async::this_coro::executor, ms};  
    co_await tim.async_wait(async::use_op);  
    printf("Hello world\n");  
}  
  
async::main co_main(int argc, char* argv[])  
{  
    delayed_print(std::chrono::milliseconds(50));  
    co_return 0;  
}  
```  
Under clang-16/Ubuntu, fails with  
  
```  
[build] /home/ruben/workspace/async/main.cpp:12:17: error: implicit instantiation of undefined template 'boost::async::promise<void>'  
[build] async::detached delayed_print(std::chrono::milliseconds ms)  
```  
  
Upon adding the relevant include, keeps failing with  
  
```  
[build] /home/ruben/workspace/async/main.cpp:13:17: error: no viable conversion from returned value of type 'promise<void>' to function return type 'async::detached'  
[build] async::detached delayed_print(std::chrono::milliseconds ms)  
```  
  
These snippets must be built as part of the library to prevent code rotting.
