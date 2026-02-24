# #257 - async_receive causes corruption/segfaults when used with io_context::executor_type [Closed]

> Username: Chaosvex  
> Created at: 2025-05-22 21:42:39 UTC  
> Updated at: 2025-05-29 13:00:41 UTC  
> Closed at: 2025-05-24 01:11:31 UTC  
> Url: https://github.com/boostorg/redis/issues/257  

Version: 1.88  
  
I'm trying to obviate the polymorphic executor (`any_io_executor`) in favour of `io_context::executor_type` and have it working fine with Boost.MySQL and Boost.Beast and it appears to mostly work with Boost.Redis but I'm encountering corruption and segfaults when calling `async_receive` on `boost::redis::connection` or `boost::redis::basic_connection<asio::io_context::executor_type>`.  
  
As a minimal example, I've taken the cpp20 subscriber example (https://github.com/boostorg/redis/blob/develop/example/cpp20_subscriber.cpp) and made minimal changes to replace the executor with `asio::io_context::executor_type`.  
  
These are the types:  
```cpp  
using executor = boost::asio::io_context::executor_type;  
  
template<typename ret_type>  
using awaitable =  
    asio::awaitable<ret_type, executor>;  
  
constexpr asio::use_awaitable_t<executor> use_awaitable(0, 0, 0);  
```  
  
This is the line that causes corruption or segfaults:  
```cpp  
co_await conn->async_receive(asio::redirect_error(use_awaitable, ec));  
```  
  
Any guidance is appreciated, thanks!

---

## Comment 1

> Username: Chaosvex  
> Created at: 2025-05-23 00:16:03 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2902924913  

With a little further investigation, the problem might run deeper than just a single function.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-05-23 10:05:55 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2903937176  

In principle, this should be supported. Could you please provide any stacktrace you found during your investigation? From hindsight, your code looks OK.

---

## Comment 3

> Username: criatura2  
> Created at: 2025-05-23 11:13:46 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2904094740  

I have reviewed the implementation around the receive channel and it looks fine. The function used to consume pushes also look fine [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/impl/response.ipp#L14). And we also have  a stress test for push-consuming [here](https://github.com/boostorg/redis/blob/e8b13bd7a0b67d22a5358fce5b621b84ee7090c4/test/test_conn_echo_stress.cpp#L46). Perhaps the stress test could be converted to use the `io_context::executor`.  
  
To investigate the issue we can also remove synchronous consumption of pushes [here](https://github.com/boostorg/redis/blob/e8b13bd7a0b67d22a5358fce5b621b84ee7090c4/example/cpp20_subscriber.cpp#L68) and replace `consume_one` with `resp.value().clear()`.

---

## Comment 4

> Username: Chaosvex  
> Created at: 2025-05-24 01:11:31 UTC  
> Updated at: 2025-05-24 01:13:52 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2906245397  

The problem was in my code - it turned out that the `CompletionToken` `(asio::consign(asio::detached, redis))` was going out of scope after calling `async_run` as I'd incorrectly assumed it was taken by value, as with the logger. I was configuring the connection in a function and returning it - it seems this is a no-go with the current design.  
  
I'm not sure why building the example as a minimal repro but with redefining the executor type exhibited the same segfaults, but I did have to play around with CMake to get it to link properly as the vcpkg package doesn't work out of the box (it has missing deps), so perhaps something funky was going on there. I rewrote the CMake project for it and it was fine afterwards.  
  
Thanks for the responses and apologies for any time wasted.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-05-24 14:20:46 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2906858398  

Hm, `async_run` should make a decay copy of the completion token through the usual `asio::async_initiate` mechanism (which I think it's currently doing). Out of curiosity, could you share a minimal version of the code calling `async_run` that was causing the corruption?

---

## Comment 6

> Username: Chaosvex  
> Created at: 2025-05-29 09:49:31 UTC  
> Updated at: 2025-05-29 09:55:17 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2918884612  

@anarthal Sorry for the delay. Went to reproduce it and realised I misspoke, it was the config going out of scope. To provide context, I was trying to compartmentalise creating the config/connection in the same way that I've done with Boost.MySQL. Boost.MySQL takes a copy/move of the config but I completely overlooked that Boost.Redis does not.   
  
This might be an idea for a non-breaking API improvement?  
  
Here's the code that was being used (ignore them being coroutines, that's just detritus):  
  
 ```cpp  
auto Service::redis_connection(const po::variables_map& args,  
                               asio::io_context& ctx)  
                               -> awaitable<std::unique_ptr<redis::connection>> {  
    redis::config cfg {  
        .addr {  
            .host = args["redis.host"].as<std::string>(),  
            .port = std::to_string(args["redis.port"].as<std::uint16_t>()),        
        },  
        .username = args["redis.user"].as<std::string>(),  
        .password = args["redis.pass"].as<std::string>(),  
    };  
  
    auto log_level = redis::logger::level::disabled;  
    const auto debug = args["redis.debug"].as<bool>();  
      
    if(debug) {  
        log_level = redis::logger::level::debug;  
    }  
  
    redis::logger logger(log_level);  
    auto redis = std::make_unique<redis::connection>(ctx);  
    redis->async_run(cfg, logger, asio::consign(asio::detached, redis.get()));  
    co_return redis;  
}  
  
auto Service::mysql_pool(const po::variables_map& args,  
                         asio::io_context& ctx) -> awaitable<mysql::connection_pool>  {  
    mysql::pool_params params {  
        .username = args["mysql.user"].as<std::string>(),  
        .password = args["mysql.pass"].as<std::string>(),  
        .database = args["mysql.db"].as<std::string>()  
    };  
  
    const auto host = args["mysql.host"].as<std::string>();  
    const auto port = args["mysql.port"].as<std::uint16_t>();  
    params.server_address.emplace_host_and_port(host, port);  
    co_return mysql::connection_pool(ctx, std::move(params));  
}  
```

---

## Comment 7

> Username: mzimbres  
> Created at: 2025-05-29 12:44:40 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2919284280  

The config object is copied internally by `async_run` [here](https://github.com/boostorg/redis/blob/b58e4f94de7e495a3195ff8a986e790f42060448/include/boost/redis/connection.hpp#L567) so it does not matter  if it goes out of scope.  
  
The logger on the other hand might cause problems because it keeps a `string_view` internally [here](https://github.com/boostorg/redis/blob/b58e4f94de7e495a3195ff8a986e790f42060448/include/boost/redis/logger.hpp#L135). This was an unfortunate choice. I will open an issue to fix it.

---

## Comment 8

> Username: anarthal  
> Created at: 2025-05-29 13:00:40 UTC  
> Url: https://github.com/boostorg/redis/issues/257#issuecomment-2919321985  

Oh I see the problem now. @Chaosvex thanks for sharing.  
  
BTW, `async_run` is doing side effects during initiation, which it should not (this would break `asio::deferred`). I don't think this has any real impact, but still.
