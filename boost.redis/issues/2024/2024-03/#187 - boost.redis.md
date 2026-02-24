# #187 - boost.redis [Closed]

> Username: zxyAcmen  
> Created at: 2024-03-14 01:59:30 UTC  
> Updated at: 2025-11-18 13:28:40 UTC  
> Closed at: 2025-11-18 13:28:40 UTC  
> Url: https://github.com/boostorg/redis/issues/187  

#include <iostream>  
#include <string>  
  
#include "json/json.h"  
  
#include "boost/redis/src.hpp"  
// #include <boost/lexical_cast.hpp>  
#include <boost/thread/locks.hpp>  
#include <boost/thread/shared_mutex.hpp>  
  
#include <boost/redis/connection.hpp>  
#include <boost/redis/config.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/use_awaitable.hpp>  
#include <boost/asio/io_context.hpp>  
  
  
  
#include <boost/asio/deferred.hpp>  
  
#include <boost/asio/detached.hpp>  
#include <boost/asio/consign.hpp>  
// #include <iostream>  
  
namespace asio = boost::asio;  
using boost::redis::config;  
using boost::redis::logger;  
using boost::redis::connection;  
using boost::redis::request;  
using boost::redis::response;  
  
asio::awaitable<void> co_main(config cfg)  
{  
    auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
    conn->async_run(cfg, {}, asio::consign(asio::detached, conn));  
    std::cout << "host:===============" << std::endl;  
    // A request containing only a ping command.  
    request req;  
    req.push("PING", "Hello world");  
  
    // Response where the PONG response will be stored.  
    response<std::string> resp;  
  
    // Executes the request.  
    co_await conn->async_exec(req, resp, asio::deferred);  
    conn->cancel();  
  
    std::cout << "PING: " << std::get<0>(resp).value() << std::endl;  
    sleep(1000*1000*1000);  
}  
  
auto main(int argc, char * argv[]) -> int  
{  
  
try {  
    config cfg;  
  
    if (argc == 5) {  
        cfg.addr.host = argv[1];  
        cfg.addr.port = argv[2];  
        // cfg.username = argv[3];  
        cfg.password = argv[4];  
        cfg.use_ssl = true;  
    }  
    std::cout << "host: " << cfg.addr.host   
        << "port: " << cfg.addr.port  
        << "name: " << cfg.username   
        << "passwd: " << cfg.password  
    << std::endl;  
    asio::io_context ioc;  
    asio::co_spawn(ioc, co_main(cfg), [](std::exception_ptr p) {  
        if (p) {  
            std::rethrow_exception(p);  
        }  
    });  
    ioc.run();  
    sleep(1000*1000*1000);  
} catch (std::exception const& e) {  
    std::cerr << "(main)== " << e.what() << std::endl;  
    return 1;  
}  
}  
  
This problem has been consistently occurring  
  
(main)== Operation canceled [system:125]  
![image](https://github.com/boostorg/redis/assets/20756430/d141e643-a33c-4229-94b3-63b8d0f3002c)  
  
  
  
  
Can you provide a complete example? This would be a very friendly way for new users of the boost library？ thanks

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-03-14 14:01:12 UTC  
> Updated at: 2024-03-14 14:02:45 UTC  
> Url: https://github.com/boostorg/redis/issues/187#issuecomment-1997531051  

Hi @zxyAcmen, here is a complete example (notice I am passing `boost::redis::logger::level::debug` to `async_run` to get detailed information about internal steps)  
  
```cpp  
#include <boost/redis/connection.hpp>  
#include <boost/asio/deferred.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/consign.hpp>  
#include <iostream>  
  
#include <boost/redis/src.hpp>  
  
namespace asio = boost::asio;  
using boost::redis::config;  
using boost::redis::connection;  
using boost::redis::logger;  
using boost::redis::request;  
using boost::redis::response;  
  
auto co_main(config cfg) -> asio::awaitable<void>  
{  
   auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
   conn->async_run(cfg, {logger::level::debug}, asio::consign(asio::detached, conn));  
  
   // A request containing only a ping command.  
   request req;  
   req.push("PING", "Hello world");  
  
   // Response where the PONG response will be stored.  
   response<std::string> resp;  
  
   // Executes the request.  
   co_await conn->async_exec(req, resp, asio::deferred);  
   conn->cancel();  
  
   std::cout << "PING: " << std::get<0>(resp).value() << std::endl;  
}  
  
auto main(int argc, char * argv[]) -> int  
{  
   try {  
      config cfg;  
  
      asio::io_context ioc;  
      asio::co_spawn(ioc, co_main(cfg), [](std::exception_ptr p) {  
         if (p)  
            std::rethrow_exception(p);  
      });  
      ioc.run();  
  
   } catch (std::exception const& e) {  
      std::cerr << "(main) " << e.what() << std::endl;  
      return 1;  
   }  
}  
```

---

## Comment 2

> Username: hungptit  
> Created at: 2024-10-08 13:12:38 UTC  
> Updated at: 2024-10-08 13:51:46 UTC  
> Url: https://github.com/boostorg/redis/issues/187#issuecomment-2399811608  

@zxyAcmen I tried your sample code and the PING command does not seem to work (the sync example works fine). I can ping the test server using the redis-cli command. What do I miss?  
  
```text  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 117 bytes written.  
(Boost.Redis) reader-op: 181 bytes read.  
(Boost.Redis) hello-op: Success  
PING: Hello world  
(Boost.Redis) ping_op: error/cancelled (2).  
(Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Operation canceled (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
```
