# #182 - second call to async_exec hangs and never returns [Closed]

> Username: rotrida  
> Created at: 2024-01-26 17:50:02 UTC  
> Updated at: 2024-02-17 12:15:50 UTC  
> Closed at: 2024-02-17 12:15:50 UTC  
> Url: https://github.com/boostorg/redis/issues/182  

Hi,  
  
I'm planning to replace my redis connection with boost.redis using coroutines. I was testing the code bellow but I noticed that it aways freezes at the second call to async_exec. Any idea what I'm doing wrong? My plan is to keep the connection to the database open and re-use it always when is necessary.  
  
auto  
receiver(std::shared_ptr<connection> conn) -> asio::awaitable<void>  
{  
   request auth_req;  
   auth_req.push("AUTH", "my_psw");  
   boost::redis::response<std::string> auth_resp;  
   co_await conn->async_exec(auth_req, auth_resp, boost::asio::use_awaitable);  
   std::cout << "AUTH: " << std::get<0>(auth_resp).value() << std::endl;  
   std::get<0>(auth_resp).value().clear();  
  
   request ping_req;  
   auth_req.push("PING");  
   boost::redis::response<std::string> ping_resp;  
   co_await conn->async_exec(ping_req, ping_resp, boost::asio::use_awaitable);  
   std::cout << "PING: " << std::get<0>(ping_resp).value() << std::endl;  
   std::get<0>(ping_resp).value().clear();

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-01-26 22:13:09 UTC  
> Url: https://github.com/boostorg/redis/issues/182#issuecomment-1912764194  

**TL;DR**: Pass the credentials to `async_run` and you should be ok  
  
```cpp  
boost::redis::config cfg;  
cfg.username = "username";  
cfg.password = "password";  
cfg.addr.host = "host";  
cfg.addr.port = "port";  
  
conn.async_run(cfg, {}, [](auto ec) { });  
```  
  
> I was testing the code bellow but I noticed that it aways freezes at the second call to async_exec. Any idea what I'm doing wrong?  
  
I believe the problem is that the `HELLO` command that is sent automatically by Boost.Redis is failing due to authentication issues.  Setting the credentials as shown above will fix that. If that doesn't work, please call `async_run` with debug logging level.  
  
```cpp  
conn.async_run(cfg, {boost::redis::logger::level::debug}, [](auto ec) { });  
```  
  
and sent it here so I can analyse the problem.  
  
> My plan is to keep the connection to the database open and re-use it always when is necessary.  
  
Yes, that is the correct way of using Boost.Redis. Most of the time you only need one connection.

---

## Comment 2

> Username: rotrida  
> Created at: 2024-01-29 09:53:05 UTC  
> Url: https://github.com/boostorg/redis/issues/182#issuecomment-1914335618  

Hi Marcelo,  
  
I changed the code (bellow) but the situation persists. I noted that if I remove the need of password to connect (requirepass my_psw), everything works. I'm not using username on the configuration, btw.  
  
 I also collected the debug info for you to check.  
  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 122 bytes written.  
(Boost.Redis) reader-op: 310 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
(Boost.Redis) check-timeout-op: Response error. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Success (async_check_timeout).  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 108 bytes written.  
(Boost.Redis) reader-op: 276 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
  
```  
auto main(int argc, char * argv[]) -> int  
{  
   try {  
      config cfg;  
  
      //if (argc == 3) {  
      //   cfg.addr.host = argv[1];  
      //   cfg.addr.port = argv[2];  
      //}  
  
      cfg.addr.host = "127.0.0.1";  
      cfg.addr.port = "6379";  
      cfg.password = "my_psw";  
      cfg.database_index = 2;  
  
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
auto main() -> int  
{  
   std::cout << "Requires coroutine support." << std::endl;  
   return 0;  
}  
  
// Receives server pushes.  
auto  
receiver(std::shared_ptr<connection> conn) -> asio::awaitable<void>  
{  
   {  
      request ping_req;  
      ping_req.push("PING");  
      boost::redis::response<std::string> ping_resp;  
      co_await conn->async_exec(ping_req, ping_resp, boost::asio::use_awaitable);  
      std::cout << "PING: " << std::get<0>(ping_resp).value() << std::endl;  
      std::get<0>(ping_resp).value().clear();  
   }  
  
   {  
      request ping_req;  
      ping_req.push("PING");  
      boost::redis::response<std::string> ping_resp;  
      co_await conn->async_exec(ping_req, ping_resp, boost::asio::use_awaitable);  
      std::cout << "PING2: " << std::get<0>(ping_resp).value() << std::endl;  
      std::get<0>(ping_resp).value().clear();  
   }  
  
   request req;  
   req.push("SUBSCRIBE", "channel");  
  
   generic_response resp;  
   conn->set_receive_response(resp);  
  
   // Loop while reconnection is enabled  
   while (conn->will_reconnect()) {  
  
      // Reconnect to the channels.  
      co_await conn->async_exec(req, ignore, asio::deferred);  
  
      // Loop reading Redis pushs messages.  
      for (error_code ec;;) {  
         // First tries to read any buffered pushes.  
         conn->receive(ec);  
         if (ec == error::sync_receive_push_failed) {  
            ec = {};  
            co_await conn->async_receive(asio::redirect_error(asio::use_awaitable, ec));  
         }  
  
         if (ec)  
            break; // Connection lost, break so we can reconnect to channels.  
  
         std::cout  
            << resp.value().at(1).value  
            << " " << resp.value().at(2).value  
            << " " << resp.value().at(3).value  
            << std::endl;  
  
         consume_one(resp);  
      }  
   }  
}  
  
auto co_main(config cfg) -> asio::awaitable<void>  
{  
   auto ex = co_await asio::this_coro::executor;  
   auto conn = std::make_shared<connection>(ex);  
   asio::co_spawn(ex, receiver(conn), asio::detached);  
   conn->async_run(cfg, {boost::redis::logger::level::debug}, asio::consign(asio::detached, conn));  
  
   signal_set sig_set(ex, SIGINT, SIGTERM);  
   co_await sig_set.async_wait();  
  
   conn->cancel();  
}  
```  
  
Thank you very much,  
Rodrigo.

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-01-29 21:14:42 UTC  
> Url: https://github.com/boostorg/redis/issues/182#issuecomment-1915583038  

>  I noted that if I remove the need of password to connect (requirepass my_psw), everything works. I'm not using username on the configuration, btw.  
  
Then please set `default` as username (in addition to the other fields)  
  
```cpp  
 cfg.username = "default";  
 ```  
  
Let me know if it works.

---

## Comment 4

> Username: rotrida  
> Created at: 2024-01-30 09:38:10 UTC  
> Url: https://github.com/boostorg/redis/issues/182#issuecomment-1916434700  

Yep, it does work!  
  
Thanks again.
