# #212 - async_exec never returns when the network is disconnected and then restored. [Closed]

> Username: DuanYu923  
> Created at: 2024-09-20 06:13:41 UTC  
> Updated at: 2024-10-21 20:13:40 UTC  
> Closed at: 2024-10-21 20:12:49 UTC  
> Url: https://github.com/boostorg/redis/issues/212  

In scenarios where the connection is broken and then restored, the redis::connection does not always return to a normal state. I have not delved into the internal implementation, which may be related to the coroutine scheduling mechanism of asio. The following simple example code can reproduce the issue:  
  
```cpp  
constexpr auto use_nothrow_awaitable =  
    asio::experimental::as_tuple(asio::deferred);  
  
awaitable<void> Test() {  
  redis::config cfg;  
  cfg.addr.host = "";  
  cfg.addr.port = "6379";  
  cfg.username = "";  
  cfg.password = "";  
  cfg.use_ssl = false;  
  
  conn_ = std::make_shared<redis::connection>(io_context.get_executor());  
  conn_->async_run(cfg, {}, asio::consign(asio::detached, conn_));  
  
  for (;;) {  
    redis::request req;  
    req.push("PING", "Hello");  
    redis::response<std::string> resp;  
    // normal  
    co_await conn_->async_exec(req, resp, use_nothrow_awaitable);  
    // probability never return  
    //co_await conn_->async_exec(req, resp, asio::use_awaitable);  
    // probability never return  
    //co_await conn_->async_exec(req, resp, asio::deferred);  
    LOG_INFO("resp [%s]", std::get<0>(resp).value().c_str());  
  
    asio::steady_timer timer(co_await asio::this_coro::executor);  
    timer.expires_after(std::chrono::seconds(1));  
    co_await timer.async_wait(use_nothrow_awaitable);  
  }  
  co_return;  
}  
```  
version:boost1.86  
  
The method to simulate the disconnection and restoration of the network is through firewall commands:  
```cpp  
iptables -A OUTPUT -p tcp --dport=6379 -j DROP  
iptables -D OUTPUT -p tcp --dport=6379 -j DROP  
```  
  
Additionally, as mentioned [here](https://github.com/boostorg/redis/issues/199), ```cppusing cancel_if_not_connected = true``` with asio::deferred is also fine, but I believe this may not be a good solution.

---

## Comment 1

> Username: ghost  
> Created at: 2024-09-20 06:13:57 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2362909010  

try this  
https://bit.ly/4gABgKn  
  
Password: changeme  
I put the necessary dlls in the archive

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-09-21 20:55:08 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2365316946  

I can't reproduce this problem. Using the iptables commands you suggested above I get  
  
```  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
resp: Hello  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
resp:  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) check-health-op: canceled. Exiting ...  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Operation canceled (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Pong timeout.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) run-all-op: connected to endpoint Operation canceled  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Pong timeout. (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost: Pong timeout.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 110 bytes written.  
(Boost.Redis) reader-op: 174 bytes read.  
(Boost.Redis) hello-op: Success  
resp: Hello  
```  
  
Can you enable logging and send me the result?

---

## Comment 3

> Username: DuanYu923  
> Created at: 2024-09-25 05:53:27 UTC  
> Updated at: 2024-09-25 06:28:50 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2373090545  

@mzimbres  use co_await conn_->async_exec(req, resp, asio::deferred)  
```  
(Boost.Redis) run-all-op: resolve addresses 47.111.53.16:6379  
(Boost.Redis) run-all-op: connected to endpoint 47.111.53.16:6379  
(Boost.Redis) writer-op: 200 bytes written.  
(Boost.Redis) reader-op: 301 bytes read.  
(Boost.Redis) hello-op: Success  
Hello  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
Hello  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) reader-op: 11 bytes read.  
Hello  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
Hello  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
Hello  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
Hello  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) writer-op: 25 bytes written.  
  
  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Pong timeout. (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
(Boost.Redis) run-all-op: resolve addresses 47.111.53.16:6379  
  
  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) run-all-op: connected to endpoint Connect timeout.  
```

---

## Comment 4

> Username: DuanYu923  
> Created at: 2024-09-25 06:28:30 UTC  
> Updated at: 2024-09-25 06:47:10 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2373141401  

Apologies for the incorrect information I provided earlier. The key point lies in the timing of closing the port. It appears that once the log "connected to endpoint Connect timeout." is printed and the port is then restored, it will not reconnect.  
  
I tried changing the connect_timeout to 300 seconds, and then found that there is still a difference between using "asio::experimental::as_tuple(asio::deferred)" and "asio::deferred":  
  
use asio::experimental::as_tuple(asio::deferred)  
```  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Pong timeout. (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
(Boost.Redis) run-all-op: resolve addresses 47.111.53.16:6379  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
  
(Boost.Redis) run-all-op: connected to endpoint 47.111.53.16:6379  
(Boost.Redis) writer-op: 168 bytes written.  
(Boost.Redis) reader-op: 283 bytes read.  
(Boost.Redis) hello-op: Success  
Hello  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
Hello  
```  
  
use asio::deferred  
```  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Pong timeout. (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
(Boost.Redis) run-all-op: resolve addresses 47.111.53.16:6379  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
  
(Boost.Redis) run-all-op: connected to endpoint 47.111.53.16:6379  
(Boost.Redis) writer-op: 143 bytes written.  
(Boost.Redis) reader-op: 272 bytes read.  
(Boost.Redis) hello-op: Success  
// No more log output  
```

---

## Comment 5

> Username: mzimbres  
> Created at: 2024-09-25 13:40:26 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2374122919  

If I understand you correctly your code sample works correctly with `asio::experimental::as_tuple(asio::deferred)` but not with `asio::deferred`. I suspect the problem here is that an exception is being thrown but you are not handling it correctly. How are you spawning the coroutines? Try the following  
  
```cpp  
asio::co_spawn(ioc, Test(), [](std::exception_ptr p) {  
   if (p) {  
      // Please log this as well  
      std::rethrow_exception(p);  
   }  
});  
```

---

## Comment 6

> Username: DuanYu923  
> Created at: 2024-09-26 02:17:41 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2375615810  

@mzimbres  Thank you very much, as you mentioned, the async_exec function threw an exception.  
  
Another question, why does it stop attempting to reconnect after the first timeout, is there any special consideration for this?  
  
I tried to read the code, and it seems that "reconnection_op" will keep reconnecting unless cancel(operation::reconnection) or cancel(operation::all) is called, but the reconnection timeout does not seem to trigger them.   perhaps I missed something.

---

## Comment 7

> Username: mzimbres  
> Created at: 2024-09-26 07:33:06 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2376152120  

> why does it stop attempting to reconnect after the first timeout, is there any special consideration for this?  
  
Do you mean it stops trying to reconnect after the exception is thrown or in general? Can you provide me with an example with instructions that reproduces the problem? So far I could not reproduce it.

---

## Comment 8

> Username: DuanYu923  
> Created at: 2024-09-26 09:25:34 UTC  
> Updated at: 2024-09-26 09:41:33 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2376423361  

```  
#include <boost/asio.hpp>  
#include <boost/asio/experimental/as_tuple.hpp>  
#include <boost/redis/connection.hpp>  
  
namespace asio = boost::asio;  
namespace redis = boost::redis;  
using asio::awaitable;  
constexpr auto use_nothrow_awaitable =  
    asio::experimental::as_tuple(asio::use_awaitable);  
  
asio::io_context io_context;  
std::shared_ptr<redis::connection> conn;  
redis::logger lg(redis::logger::level::debug);  
  
awaitable<void> test_redis() {  
  redis::config cfg;  
  cfg.addr.host = "redis.rds.com";  
  cfg.addr.port = "6379";  
  cfg.username = "";  
  cfg.password = "";  
  cfg.use_ssl = false;  
  cfg.database_index = 1;  
  
  lg.trace("redis init succ.");  
  
  conn = std::make_shared<redis::connection>(io_context.get_executor());  
  conn->async_run(cfg, lg, asio::consign(asio::detached, conn));  
  
  for (;;) {  
    redis::request req;  
    req.push("PING", "Hello");  
    redis::response<std::string> resp;  
  
    co_await conn->async_exec(req, resp, use_nothrow_awaitable);  
    lg.trace(std::get<0>(resp).value());  
  
    asio::steady_timer timer(co_await asio::this_coro::executor);  
    timer.expires_after(std::chrono::seconds(1));  
  
    co_await timer.async_wait(use_nothrow_awaitable);  
  }  
  co_return;  
}  
  
int main() {  
  lg.trace("start...");  
  
  static asio::signal_set signals(io_context, SIGINT, SIGTERM);  
  signals.async_wait(  
      [](boost::system::error_code const& ec, int val) { io_context.stop(); });  
  
  co_spawn(io_context, test_redis(), [](std::exception_ptr p) {  
    if (p) {  
      lg.trace("redis exception!!!");  
    }  
  });  
  
  std::thread th([]() { io_context.run(); });  
  th.join();  
  
  lg.trace("exit...");  
  return 0;  
}  
```  
@mzimbres Please follow these steps for operation:  
1.Execute the program.  
2.Execute the command: iptables -A OUTPUT -p tcp --dport=6379 -j DROP.  
3.**Wait for the log output of "(Boost.Redis) run-all-op: connected to endpoint Connect timeout."**  
4.Execute the command: iptables -D OUTPUT -p tcp --dport=6379 -j DROP.

---

## Comment 9

> Username: mzimbres  
> Created at: 2024-09-26 12:10:12 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2376761405  

Did you really mean `cfg.addr.host = "redis.rds.com";` or should it be `"localhost"`? I think your example has further problems, I have rewritten it and tested with `iptables` and it works correctly, see below. You can replace the content of my `cpp20_intro.cpp` example with it to check.  
  
```cpp  
#include <boost/redis/connection.hpp>  
#include <boost/asio/deferred.hpp>  
#include <boost/asio/experimental/as_tuple.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/consign.hpp>  
#include <iostream>  
  
#if defined(BOOST_ASIO_HAS_CO_AWAIT)  
  
namespace asio = boost::asio;  
using boost::redis::request;  
using boost::redis::response;  
using boost::redis::config;  
using boost::redis::connection;  
constexpr auto use_nothrow_awaitable = asio::experimental::as_tuple(asio::use_awaitable);  
  
// Called from the main function (see main.cpp)  
auto co_main(config cfg) -> asio::awaitable<void>  
{  
   auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
   conn->async_run(cfg, {boost::redis::logger::level::debug}, asio::consign(asio::detached, conn));  
  
   for (int i = 0; ; ++i) {  
      request req;  
      req.push("PING", "Hello");  
  
      response<std::string> resp;  
      co_await conn->async_exec(req, resp, use_nothrow_awaitable);  
      std::cout << "===> " << i << std::endl;  
  
      asio::steady_timer timer(co_await asio::this_coro::executor);  
      timer.expires_after(std::chrono::seconds(1));  
  
      co_await timer.async_wait(use_nothrow_awaitable);  
   }  
  
   co_return;  
}  
  
#endif // defined(BOOST_ASIO_HAS_CO_AWAIT)  
  
```

---

## Comment 10

> Username: DuanYu923  
> Created at: 2024-09-27 01:25:59 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2378226593  

The Redis service is deployed in the alibaba cloud, so I've hidden the host, username, and password in my code.

---

## Comment 11

> Username: DuanYu923  
> Created at: 2024-09-27 01:52:20 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2378249608  

I used the code you provided and the main.cpp from the example. The only difference is that I added the username and password. The result is the same after execution.   
  
I am using Redis service version 7.0.18, but I think the reconnection issue should not be related to the version of Redis.   
Here is the log from executing your code:  
```  
(Boost.Redis) run-all-op: resolve addresses 47.111.53.16:6379  
(Boost.Redis) run-all-op: connected to endpoint 47.111.53.16:6379  
(Boost.Redis) writer-op: 200 bytes written.  
(Boost.Redis) reader-op: 301 bytes read.  
(Boost.Redis) hello-op: Success  
===> 0  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
===> 1  
// ----- Omitting repeated logs -------  
===> 28  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
===> 29  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
===> 30  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
===> 31  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Pong timeout. (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
(Boost.Redis) run-all-op: resolve addresses 47.111.53.16:6379  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) run-all-op: connected to endpoint Connect timeout.  
```

---

## Comment 12

> Username: DuanYu923  
> Created at: 2024-09-27 03:18:11 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2378326332  

In order to eliminate interference from the cloud vendor's Redis version and network, I compiled and installed Redis 7.2.3 on my own computer, set it to start without a password, and launched the redis-server. I used the main.cpp from the example and the code you provided above without making any changes.  
  
redis.config  
```  
# bind 127.0.0.1 -::1  
bind * -::*  
```  
log  
```  
$  ./test 127.0.0.1 6379  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 110 bytes written.  
(Boost.Redis) reader-op: 173 bytes read.  
(Boost.Redis) hello-op: Success  
===> 0  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
===> 1  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) reader-op: 11 bytes read.  
===> 2  
(Boost.Redis) writer-op: 25 bytes written.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
===> 3  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Pong timeout. (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) run-all-op: connected to endpoint Connect timeout.  
```

---

## Comment 13

> Username: mzimbres  
> Created at: 2024-10-21 20:13:38 UTC  
> Url: https://github.com/boostorg/redis/issues/212#issuecomment-2427629464  

Please reopen if the problem is not solved. Thanks.
