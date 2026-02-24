# #243 - It's possible to use this lib without coroutines? [Closed]

> Username: Zen0x7  
> Created at: 2025-04-19 01:52:02 UTC  
> Updated at: 2025-04-19 20:25:04 UTC  
> Closed at: 2025-04-19 20:25:03 UTC  
> Url: https://github.com/boostorg/redis/issues/243  

I'm trying multiple ways to create connections, passing a custom handler to async_run (as the third parameter), as basic as it sounds, my handler only has a print, but still, it doesn't work. Which makes me think that the library works perfectly with coroutines but not very well with async_SOMETHING operations like in asio.  
  
I intend to remove all coroutines from a system, to compare two versions of the same system and thus scrutinize the amount of cache missing (among other metrics).   
  
Is it possible to use this library without coroutines? And if so, is there a possible example?

---

## Comment 1

> Username: anarthal  
> Created at: 2025-04-19 09:26:18 UTC  
> Url: https://github.com/boostorg/redis/issues/243#issuecomment-2816628255  

AFAIK the library follows Asio's universal model, so what you're trying to do should work.  
  
For instance: https://github.com/boostorg/redis/blob/develop/example/cpp17_intro.cpp  
  
What does not work mean in your case? What behavior are you observing?  
  
Cheers,  
Ruben.

---

## Comment 2

> Username: Zen0x7  
> Created at: 2025-04-19 16:47:22 UTC  
> Url: https://github.com/boostorg/redis/issues/243#issuecomment-2816778986  

Hi Ruben,   
  
Consider the following:  
  
```c++  
template <typename... T>  
using callback_of = std::function<void(T...)>;  
  
template <typename T>  
using shared = boost::shared_ptr<T>;  
  
#define LOG(x) std::cout << x << std::endl  
  
void cache::get_connection(  
  callback_of<const shared<boost::redis::connection>&> callback  
) const {  
  LOG("[cache@get_connection] scope_in");  
  auto _conn = boost::make_shared<boost::redis::connection>(make_strand(ioc_));  
  auto self = shared_from_this();  
  
  _conn->async_run(*redis_configuration_, {}, [_conn = _conn, callback = std::move(callback), self](auto ec)  
  {  
    LOG("This should happens or not?");  
    callback(_conn);  
  });  
  
  LOG("[cache@get_connection] scope_out");  
}  
```  
  
I did severals tries, including:  
  
- The usage of `ioc_` instead of `make_strand(ioc_)`.  
- Pushing _conn to a vector to keep it alive.  
- Changing multiple times the captured vars. (Moving, not moving, using self, not using self ... and so on)  
  
But `LOG("This should happens or not?")` never happens. I expect that completition token should be executed when the connection is ready and i'm not sure if that works on that way, but is as works on async_read, async_write asio OPs.  
  
¿Why it's relevant for me? Because if i use the boost::asio::detached completition token like this:  
  
```c++  
void cache::get_connection(  
    callback_of<const shared<boost::redis::connection>&> callback) const {  
  LOG("[cache@get_connection] scope_in");  
  auto _conn = boost::make_shared<boost::redis::connection>(make_strand(ioc_));  
  _conn->async_run(*redis_configuration_, {}, boost::asio::detached);  
  callback(_conn);  
  LOG("[cache@get_connection] scope_out");  
}  
```  
  
Then the next operation will fails with exception:  
  
```c++  
void cache::has(const std::string& key,  
                const shared<boost::redis::connection>& connection,  
                callback_of<int> callback) {  
  LOG("[cache@has] scope_in");  
  std::cout << "KEY is: " << key << std::endl;  
  boost::redis::request _request;  
  boost::redis::response<int> _response;  
  _request.push("EXISTS", key);  
  connection->async_exec(  
      _request, _response,  
      [callback = std::move(callback), _response](  
          const boost::system::error_code& error, std::size_t) mutable {  
        LOG("[cache@has] connection.async_exec scope_in");  
        callback(error ? 0 : std::get<0>(_response).value());  
        LOG("[cache@has] connection.async_exec scope_out");  
      });  
  LOG("[cache@has] scope_out");  
}  
```  
  
See the logs:  
  
```  
[cache@can_invoke] get_connection scope_in  
[cache@has] scope_in  
KEY is: POST:/api/register:127.0.0.1  
[cache@has] scope_out  
[cache@can_invoke] get_connection scope_out  
[cache@get_connection] scope_out  
[cache@can_invoke] scope_out  
[kernel_call_context@check_throttle] scope_out [2 of 2]  
[kernel_call_context@start] scope_out [2 of 2]  
[kernel@call] scope_out  
[http_session@on_read] scope_out  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
terminate called after throwing an instance of 'std::bad_alloc'  
  what():  std::bad_alloc  
```  
  
The question is, i'm doing something wrong? It's by design? Dont know in this moment. I'm using 1.87.0 (i'll update my docker images with 1.88 soon).

---

## Comment 3

> Username: anarthal  
> Created at: 2025-04-19 18:30:29 UTC  
> Url: https://github.com/boostorg/redis/issues/243#issuecomment-2816817201  

async_run completes, and thus calls its callback (or resume its coroutine) when the run operation finishes. async_run is a long-running operation that completes only once the connection is cancelled. It won't call your callback when the connection is ready, because the operation hasn't completed yet.  
  
What you're having is a lifetime problem in async_exec. The operation requires that both the request and response objects must be kept alive until async_exec completes. This is trivial with coroutines, but you will need something else when you use callbacks. Try creating both request and response in the heap with a shared_ptr, and make sure they stay alive until async_exec completes.

---

## Comment 4

> Username: Zen0x7  
> Created at: 2025-04-19 20:25:03 UTC  
> Url: https://github.com/boostorg/redis/issues/243#issuecomment-2816854240  

Finally, i was able to make it work, you're right, the problem was a lifetime issue. About request and response lambda captures.  
  
I will share my code in this thread, especially if some people come with the same doubt, I am not proud of the quality of the code but it works, if someone's eye bleeds when reading it I apologize xD  
  
The header:  
  
```c++  
#ifndef COPPER_COMPONENTS_CACHE_HPP  
#define COPPER_COMPONENTS_CACHE_HPP  
  
#pragma once  
  
#include <boost/redis/connection.hpp>  
#include <copper/components/containers.hpp>  
#include <copper/components/req.hpp>  
#include <copper/components/shared.hpp>  
  
namespace copper::components {  
using namespace containers;  
  
/**  
 * Forward configuration  
 */  
class configuration;  
  
/**  
 * Cache  
 */  
class cache : public shared_enabled<cache> {  
  /**  
   * Configuration  
   */  
  shared<boost::redis::config> redis_configuration_ =  
      boost::make_shared<boost::redis::config>();  
  
  boost::asio::io_context& ioc_;  
 public:  
  /**  
   * Constructor  
   */  
  cache(boost::asio::io_context& ioc);  
  
  /**  
   * Retrieve connection  
   *  
   * @param callback  
   * @return void  
   */  
  void get_connection(  
      callback_of<const shared<boost::redis::connection>&> callback) const;  
  
  /**  
   * Determines if request can be invoked  
   *  
   * @param request  
   * @param ip  
   * @param max_requests  
   * @param callback  
   * @return void  
   */  
  void can_invoke(const req& request,  
                  const std::string& ip,  
                  int max_requests,  
                  const callback_of<bool, int>& callback) const;  
  
  /**  
   * Publish  
   *  
   * @param channel  
   * @param data  
   * @return void  
   */  
  void publish(const std::string& channel, const std::string& data) const;  
  
  /**  
   * Factory  
   *  
   * @return shared<cache>  
   */  
  static shared<cache> factory(boost::asio::io_context& ioc);  
  
 private:  
  /**  
   * Determines if cache has key  
   *  
   * @param key  
   * @param connection  
   * @param callback  
   * @return void  
   */  
  static void has(const std::string& key,  
                  const shared<boost::redis::connection>& connection,  
                  callback_of<int> callback);  
  
  /**  
   * Retrieves key's invoked times  
   *  
   * @param key  
   * @param connection  
   * @param callback  
   * @return void  
   */  
  static void counter_of(const std::string& key,  
                         const shared<boost::redis::connection>& connection,  
                         callback_of<int64_t> callback);  
  
  /**  
   * Retrieve key's expiration at  
   *  
   * @param key  
   * @param connection  
   * @param callback  
   * @return void  
   */  
  static void get_expiration_of(  
      const std::string& key,  
      const shared<boost::redis::connection>& connection,  
      callback_of<int64_t> callback);  
  
  /**  
   * Increase key  
   *  
   * @param key  
   * @param connection  
   * @return void  
   */  
  static void increase(const std::string& key,  
                       const shared<boost::redis::connection>& connection);  
  
  /**  
   * Set key  
   *  
   * @param key  
   * @param connection  
   * @return void  
   */  
  static void set(const std::string& key,  
                  const shared<boost::redis::connection>& connection);  
};  
}  // namespace copper::components  
  
#endif  
```  
  
The cpp:   
  
```c++  
#include <boost/asio/bind_cancellation_slot.hpp>  
#include <boost/asio/detached.hpp>  
#include <copper/components/cache.hpp>  
#include <copper/components/configuration.hpp>  
#include <copper/components/logger.hpp>  
#include <copper/components/response.hpp>  
#include <copper/components/url.hpp>  
#include <iostream>  
  
namespace copper::components {  
void cache::has(const std::string& key,  
                const shared<boost::redis::connection>& connection,  
                callback_of<int> callback) {  
  LOG("[cache@has] scope_in");  
  auto _request = boost::make_shared<boost::redis::request>();  
  auto _response = boost::make_shared<boost::redis::response<int>>();  
  _request->push("EXISTS", key);  
  connection->async_exec(  
      *_request, *_response,  
      [callback = std::move(callback), _response, _request](  
          const boost::system::error_code& error, std::size_t) mutable {  
        LOG("[cache@has] connection.async_exec scope_in");  
        callback(error ? 0 : std::get<0>(*_response).value());  
        LOG("[cache@has] connection.async_exec scope_out");  
      });  
  LOG("[cache@has] scope_out");  
}  
  
void cache::counter_of(const std::string& key,  
                       const shared<boost::redis::connection>& connection,  
                       callback_of<int64_t> callback) {  
  LOG("[cache@counter_of] scope_in");  
  auto _request = boost::make_shared<boost::redis::request>();  
  auto _response = boost::make_shared<boost::redis::response<int64_t>>();  
  _request->push("GET", key);  
  
  connection->async_exec(  
      *_request, *_response,  
      [callback = std::move(callback), _response, _request](  
          const boost::system::error_code& error, std::size_t) mutable {  
        LOG("[cache@counter_of] connection.async_exec scope_in");  
        callback(error ? 0 : std::get<0>(*_response).value());  
        LOG("[cache@counter_of] connection.async_exec scope_out");  
      });  
  LOG("[cache@counter_of] scope_out");  
}  
  
void cache::get_expiration_of(  
    const std::string& key,  
    const shared<boost::redis::connection>& connection,  
    callback_of<int64_t> callback) {  
  LOG("[cache@get_expiration_of] scope_in");  
  auto _request = boost::make_shared<boost::redis::request>();  
  auto _response = boost::make_shared<boost::redis::response<int64_t>>();  
  _request->push("TTL", key);  
  
  connection->async_exec(  
      *_request, *_response,  
      [callback = std::move(callback), _response, _request](  
          const boost::system::error_code& error, std::size_t) mutable {  
        LOG("[cache@get_expiration_of] connection.async_exec scope_in");  
        callback(error ? 0 : std::get<0>(*_response).value());  
        LOG("[cache@get_expiration_of] connection.async_exec scope_out");  
      });  
  LOG("[cache@get_expiration_of] scope_out");  
}  
  
void cache::increase(const std::string& key,  
                     const shared<boost::redis::connection>& connection) {  
  LOG("[cache@increase] scope_in");  
  auto _request = boost::make_shared<boost::redis::request>();  
  auto _response = boost::make_shared<boost::redis::response<std::string>>();  
  _request->push("INCR", key);  
  connection->async_exec(  
      *_request, *_response,  
      [_request, _response](const boost::system::error_code& error,  
                            std::size_t) {  
        LOG("[cache@increase] connection.async_exec scope_in");  
        LOG("[cache@increase] connection.async_exec scope_out");  
      });  
  LOG("[cache@increase] scope_out");  
}  
  
void cache::set(const std::string& key,  
                const shared<boost::redis::connection>& connection) {  
  LOG("[cache@set] scope_in");  
  auto _request = boost::make_shared<boost::redis::request>();  
  auto _response = boost::make_shared<boost::redis::response<std::string>>();  
  _request->push("SET", key, 1, "EX", 5);  
  connection->async_exec(  
      *_request, *_response,  
      [_request, _response](const boost::system::error_code& error,  
                            std::size_t) {  
        LOG("[cache@set] connection.async_exec scope_in");  
        LOG("[cache@set] connection.async_exec scope_out");  
      });  
  LOG("[cache@set] scope_out");  
}  
  
void cache::can_invoke(const req& request,  
                       const std::string& ip,  
                       const int max_requests,  
                       const callback_of<bool, int>& callback) const {  
  LOG("[cache@can_invoke] scope_in");  
  const auto _url = url_from_request(request);  
  std::string _key = request.method_string();  
  _key.append(":");  
  _key.append(_url);  
  _key.append(":");  
  _key.append(ip);  
  get_connection([=](const shared<boost::redis::connection>& conn) mutable {  
    LOG("[cache@can_invoke] get_connection scope_in");  
    has(_key, conn, [=](const int exists) mutable {  
      LOG("[cache@can_invoke] get_connection has scope_in");  
  
      LOG("[cache@can_invoke] get_connection has exists: " << exists);  
      if (exists != 0) {  
        counter_of(_key, conn, [=](int64_t count) mutable {  
          LOG("[cache@can_invoke] get_connection has counter_of scope_in");  
          increase(_key, conn);  
          count++;  
  
          if (count <= max_requests) {  
            callback(true, 0);  
          } else {  
            get_expiration_of(_key, conn, [=](const int64_t ttl) {  
              LOG("[cache@can_invoke] get_connection has counter_of "  
                  "get_expiration_of scope_in");  
              callback(false, static_cast<int>(ttl));  
              LOG("[cache@can_invoke] get_connection has counter_of "  
                  "get_expiration_of scope_out");  
            });  
          }  
          LOG("[cache@can_invoke] get_connection has counter_of scope_out");  
        });  
      } else {  
        set(_key, conn);  
        callback(true, 0);  
      }  
      LOG("[cache@can_invoke] get_connection has scope_out");  
    });  
  
    LOG("[cache@can_invoke] get_connection scope_out");  
  });  
  LOG("[cache@can_invoke] scope_out");  
}  
  
cache::cache(boost::asio::io_context& ioc) : ioc_(ioc) {  
  LOG("[cache@cache] scope_in");  
  redis_configuration_ = boost::make_shared<boost::redis::config>();  
  
  const std::string _redis_port =  
      std::to_string(std::stoi(dotenv::getenv("REDIS_PORT", "6379")));  
  
  redis_configuration_->addr = boost::redis::address{  
      dotenv::getenv("REDIS_HOST", "127.0.0.1"), _redis_port};  
  
  redis_configuration_->health_check_interval = std::chrono::seconds{  
      std::stoi(dotenv::getenv("REDIS_HEALTH_CHECK_INTERVAL", "60"))};  
  
  redis_configuration_->connect_timeout = std::chrono::seconds{  
      std::stoi(dotenv::getenv("REDIS_CONNECTION_TIMEOUT", "60"))};  
  
  redis_configuration_->reconnect_wait_interval = std::chrono::seconds{  
      std::stoi(dotenv::getenv("REDIS_RECONNECTION_WAIT_INTERVAL", "3"))};  
  
  redis_configuration_->clientname =  
      dotenv::getenv("REDIS_CLIENT_NAME", "copper");  
  
  LOG("[cache@cache] scope_out");  
}  
  
void cache::get_connection(  
    callback_of<const shared<boost::redis::connection>&> callback) const {  
  LOG("[cache@get_connection] scope_in");  
  auto _conn = boost::make_shared<boost::redis::connection>(make_strand(ioc_));  
  auto self = shared_from_this();  
  _conn->async_run(*redis_configuration_, {},  
                   boost::asio::consign(boost::asio::detached, _conn));  
  callback(_conn);  
  LOG("[cache@get_connection] scope_out");  
}  
  
void cache::publish(const std::string& channel, const std::string& data) const {  
  LOG("[cache@publish] scope_in");  
  get_connection([=](const shared<boost::redis::connection>& conn) {  
    auto _request = boost::make_shared<boost::redis::request>();  
    _request->push("PUBLISH", channel, data);  
    conn->async_exec(  
        *_request, boost::redis::ignore,  
        [_request](const boost::system::error_code& error, std::size_t) {});  
  });  
  LOG("[cache@publish] scope_out");  
}  
  
shared<cache> cache::factory(boost::asio::io_context& ioc) {  
  return boost::make_shared<cache>(ioc);  
}  
}  // namespace copper::components  
```  
  
Thanks @anarthal
