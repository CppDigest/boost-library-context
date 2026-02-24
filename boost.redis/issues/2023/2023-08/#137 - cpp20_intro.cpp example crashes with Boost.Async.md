# #137 - cpp20_intro.cpp example crashes with Boost.Async [Open]

> Username: mzimbres  
> Created at: 2023-08-13 08:52:05 UTC  
> Updated at: 2023-08-13 08:52:05 UTC  
> Url: https://github.com/boostorg/redis/issues/137  

The example below will crash with Boost.Async  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/async.hpp>  
#include <boost/redis.hpp>  
#include <iostream>  
  
#include <boost/redis/src.hpp>  
  
namespace async = boost::async;  
namespace asio = boost::asio;  
  
using boost::redis::request;  
using boost::redis::response;  
using boost::redis::config;  
using boost::redis::connection;  
  
async::detached run(std::shared_ptr<connection> conn)  
{  
   co_await conn->async_run({}, {}, async::use_op);  
}  
  
async::main co_main(int argc, char **argv)  
{  
   auto conn = std::make_shared<connection>(co_await async::this_coro::executor);  
  
   run(conn);  
  
   request req;  
   req.push("PING", "Hello world");  
  
   response<std::string> resp;  
  
   co_await conn->async_exec(req, resp, async::use_op);  
   conn->cancel();  
  
   std::cout << "PING: " << std::get<0>(resp).value() << std::endl;  
   co_return 0;  
}  
```  
  
Output:  
  
```  
debian2[0]$ ./example/boost_async_example_http   
(Boost.Redis) Resolve results: 127.0.0.1:6379  
(Boost.Redis) Connected to endpoint: 127.0.0.1:6379  
(Boost.Redis) Bytes written: 140  
(Boost.Redis) Hello: Success  
PING: Hello world  
(Boost.Redis) Connection lost: Operation canceled  
munmap_chunk(): invalid pointer  
Aborted  
```
