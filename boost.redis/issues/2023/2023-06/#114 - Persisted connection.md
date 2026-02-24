# #114 - Persisted connection... [Closed]

> Username: cyboy22  
> Created at: 2023-06-16 10:45:20 UTC  
> Updated at: 2023-07-01 10:59:24 UTC  
> Closed at: 2023-07-01 10:59:24 UTC  
> Url: https://github.com/boostorg/redis/issues/114  

Hi,   
  
May I apologise in advance for any lack of understanding.  
I created a connection to the cache and successfully received a PING response from the ping_check() coroutine in this call (see below)  
  
 awaitable<bool> Cache_session::connect_() noexcept {  
  
        auto result = false;  
        auto executor = co_await this_coro::executor;  
  
        try {  
  
            config cfg;  
            cfg.addr.host = get_environment_variable(cache_host_address);  
            cfg.addr.port = get_environment_variable(cache_host_port);  
            cfg.health_check_interval = std::chrono::seconds{stoi(get_environment_variable(cache_health_check_interval))};  
            cache_ptr_ = std::make_shared<boost::redis::connection>(executor);  
            cache_ptr_->async_run(cfg, {boost::redis::logger::level::crit}, consign(detached, cache_ptr_));  
            result = co_await co_spawn(executor, ping_check(), use_awaitable);  
            /*  
            result = co_await co_spawn(executor, key_value_checks(), use_awaitable);  
            result = co_await co_spawn(executor, hash_checks(), use_awaitable);  
            result = co_await co_spawn(executor, set_checks(), use_awaitable);  
            result = true;  
            */  
  
        } catch (std::exception& e) {  
  
            handle_exception(boost::log::v2_mt_posix::trivial::fatal, e.what());  
        }  
  
        co_return result;  
    }  
  
awaitable<bool> Cache_session::ping_check() noexcept {  
  
        auto result = false;  
  
        try {  
  
            request req;  
            req.push("PING", cache_ping_message);  
            response<std::string> resp;  
            co_await cache_ptr_->async_exec(req, resp, use_awaitable);  
            if (cache_ping_message != std::get<0>(resp).value()) {  
  
                handle_exception(boost::log::v2_mt_posix::trivial::fatal, cache_ping_check_failed);  
  
            }  
  
            result = true;  
  
        } catch (const std::exception& e) {  
  
            handle_exception(boost::log::v2_mt_posix::trivial::fatal, e.what());  
        }  
  
        co_return result;  
    }  
  
   I then make the same call to ping_check() coroutine from another part of the code using :  
     
              result = co_await co_spawn(executor,  
                                       cache_session_ptr_->ping_check(),  
                                       use_awaitable);  
                                         
    On the second call I get a segmentation fault at the line:  
      
            co_await cache_ptr_->async_exec(req, resp, use_awaitable);  
              
    Where it was previously successful.  
      
    Can you suggest where there is a gap in my understanding as to why the second call is unsuccessful ? - thanks!

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-06-16 11:04:10 UTC  
> Url: https://github.com/boostorg/redis/issues/114#issuecomment-1594503596  

Can you share some code so I can investigate? Also, notice the connection class has built it health-checks so in gneral you don't have to perform pings manually.

---

## Comment 2

> Username: cyboy22  
> Created at: 2023-06-16 11:42:32 UTC  
> Url: https://github.com/boostorg/redis/issues/114#issuecomment-1594544509  

This is the call which encapsulates the issue...  
  
awaitable<bool> Server::connect_to_cache() noexcept {  
  
        auto result = false;  
        auto executor = co_await this_coro::executor;  
  
        try {  
  
            cache_session_ptr_ = std::make_shared<Cache_session>();  
            result = co_await co_spawn(executor,  
                                       cache_session_ptr_->connect_(),  
                                       use_awaitable);  
              
            result = co_await co_spawn(executor,  
                                       cache_session_ptr_->ping_check(),  
                                       use_awaitable);  
              
  
            if (!result)  
                handle_exception(boost::log::v2_mt_posix::trivial::fatal,  
                                 cache_connection_failed);  
  
            co_spawn(executor, audit(severity_level::info,  
                                     cache_connection_succeeded), detached);  
  
            result = true;  
  
        } catch (std::exception& e) {  
  
            handle_exception(boost::log::v2_mt_posix::trivial::fatal, e.what());  
        }  
  
        co_return result;  
    }  
  
Within the first call to cache_session_ptr_->connect() the call to ping_check() works (I was just using PING to verify there were no issues interacting with the interface and would remove it later) - in the call thereafter it does not - is it possible that the executor that was used in std::make_shared<boost::redis::connection>(executor) has gone out of scope?

---

## Comment 3

> Username: cyboy22  
> Created at: 2023-06-16 23:53:52 UTC  
> Url: https://github.com/boostorg/redis/issues/114#issuecomment-1595492675  

Hi - please let me know if you need any further info (a snippet of code for a "long-lived" connection would be of great help)

---

## Comment 4

> Username: mzimbres  
> Created at: 2023-06-17 07:40:17 UTC  
> Url: https://github.com/boostorg/redis/issues/114#issuecomment-1595659368  

> Within the first call to cache_session_ptr_->connect() the call to ping_check() works (I was just using PING to verify there were no issues interacting with the interface and would remove it later) - in the call thereafter it does not -  
  
Unless you have disabled reconnection, the function `async_run` should be called only once in your program. Also, you should not call it again before the previous call has completed. If you want to wait for it to complete replace `net::detached` with `net::use_awaitable` for example.   
  
If that does not work, please send a minimal example with which I can reproduce the problem.

---

## Comment 5

> Username: mzimbres  
> Created at: 2023-06-24 07:18:11 UTC  
> Url: https://github.com/boostorg/redis/issues/114#issuecomment-1605295914  

Did my comment above helped you any further, can we close the issue?

---

## Comment 6

> Username: cyboy22  
> Created at: 2023-06-27 00:47:37 UTC  
> Url: https://github.com/boostorg/redis/issues/114#issuecomment-1608537984  

Hi - yes it did - please go ahead and close the issue - thanks!
