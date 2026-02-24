# #216 - Any suggestions on how to deal with thread-safety of connection? [Closed]

> Username: bruno-viva  
> Created at: 2024-10-14 00:36:21 UTC  
> Updated at: 2024-10-14 21:30:24 UTC  
> Closed at: 2024-10-14 21:30:24 UTC  
> Url: https://github.com/boostorg/redis/issues/216  

Hi!  
  
When using C++17, I need to make sure I only access the `connection` object from the same thread.   
  
If I want to execute operations on the connection and handle the results later, I have to post it to the `io_context`, something like this:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
#include <boost/redis/connection.hpp>  
#include <boost/redis/src.hpp>  
#include <iostream>  
#include <semaphore>  
  
void SetKey(std::string_view key, std::string_view value,  
    boost::redis::connection& connection, std::counting_semaphore<1>& semaphore,  
    boost::asio::io_context& io_context) {  
  auto request = std::make_shared<boost::redis::request>();  
  request->push("SET", key, value);  
  auto response = std::make_shared<boost::redis::response<std::string>>();  
  io_context.post([&connection, request, response, &semaphore]() {  
    connection.async_exec(  
        *request, *response,  
        [request, response, &semaphore]  
            (const boost::system::error_code& error, std::size_t) {  
          if (error) {  
            std::cout << "Failed to send SET command to Redis server: " << error.to_string() << std::endl;  
          } else {  
            std::cout << "Response: " << std::get<0>(*response).value() << std::endl;  
          }  
          semaphore.release();  
        });  
  });  
}  
  
int main() {  
  boost::asio::io_context io_context{1};  
  std::thread thread;  
  
  // Set up.  
  boost::redis::connection connection(io_context);  
  boost::redis::config config;  
  connection.async_run(config, {boost::redis::logger::level::warning},   
    [](const boost::system::error_code& error) {  
    if (error) {  
      std::cout << "Redis server setup error: " << error.to_string() << std::endl;  
    }  
  });  
  thread = std::thread([&io_context] {  
    std::cout << "RedisGlobalCache running" << std::endl;  
    io_context.run();  
    std::cout << "RedisGlobalCache finished" << std::endl;  
  });  
  
  std::counting_semaphore<1> semaphore(0);  
  SetKey("key1", "value1", connection, semaphore, io_context);  
    
  // Somewhat later I will check the result...  
  semaphore.acquire();  
  
  io_context.post([&connection] { connection.cancel(); });  
  thread.join();  
  return 0;  
}  
```  
  
Is there a better way to do this? Does that mean I also can't have more than 1 thread in my `io_context`?  
  
Thanks in advance!

---

## Comment 1

> Username: anarthal  
> Created at: 2024-10-14 08:57:33 UTC  
> Url: https://github.com/boostorg/redis/issues/216#issuecomment-2410504334  

I think you have two options here:  
  
1.-Go fully single-threaded (like Node.js). In this case, I'd advise you to not use `std::thread` and have `main` run the context:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
#include <boost/redis/connection.hpp>  
#include <boost/redis/src.hpp>  
#include <iostream>  
#include <semaphore>  
  
void SetKey(std::string_view key, std::string_view value,  
    boost::redis::connection& connection) {  
  auto request = std::make_shared<boost::redis::request>();  
  request->push("SET", key, value);  
  auto response = std::make_shared<boost::redis::response<std::string>>();  
  connection.async_exec(  
      *request,  
      *response,  
      [request, response, &connection] (const boost::system::error_code& error, std::size_t) {  
          if (error) {  
            std::cout << "Failed to send SET command to Redis server: " << error.to_string() << std::endl;  
          } else {  
            std::cout << "Response: " << std::get<0>(*response).value() << std::endl;  
          }  
      }  
      connection.cancel();  
  );  
}  
  
int main() {  
  boost::asio::io_context io_context{1};  
  std::thread thread;  
  
  // Set up.  
  boost::redis::connection connection(io_context);  
  boost::redis::config config;  
  connection.async_run(config, {boost::redis::logger::level::warning},   
    [](const boost::system::error_code& error) {  
    if (error) {  
      std::cout << "Redis server setup error: " << error.to_string() << std::endl;  
    }  
  });  
  
  SetKey("key1", "value1", connection, semaphore);  
  
  io_context.run();  
  return 0;  
}  
```  
  
This is using "work counting" in Boost.Asio. io_context::run won't return until all outstanding I/O operations have finished. In your example, this means it won't return until you've called `cancel` on your connection.  
  
2. Go multi-threaded. I'd advise to use `asio::thread_pool` instead of `io_context` However, this requires extra care. The way to go is using [strands](https://live.boost.org/doc/libs/1_86_0/doc/html/boost_asio/overview/core/strands.html). I'd advise to first build it single-threaded, then switch to multi-threaded only after efficiency measuring.  
  
Note: `io_context.post(f)` is deprecated. The new way of writing it is `asio::post(asio::bind_executor(io_context.get_executor(), f))`

---

## Comment 2

> Username: bruno-viva  
> Created at: 2024-10-14 14:45:22 UTC  
> Url: https://github.com/boostorg/redis/issues/216#issuecomment-2411474053  

Thanks @anarthal !  
  
1) Thanks for the suggestion. The problem is that I do want the Redis operation to be async, as the way this integrates into my project will be something like: "User Request" -> "Executes Redis" -> "Reply later with result". And I can't really block the execution with the `run` (that is why I have it in another thread), and would like to reuse the same connection object.  
  
2) I see, it does seem that strands would be the best way to solve the problem if having more than one thread!  
  
Thanks for pointing out about the deprecated function!

---

## Comment 3

> Username: anarthal  
> Created at: 2024-10-14 15:33:43 UTC  
> Url: https://github.com/boostorg/redis/issues/216#issuecomment-2411611117  

Hm, is the rest of your program using Asio? Or is it just the part using Redis? In the latter case, I'd suggest to use something like the following:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
  
#include <cstddef>  
#include <future>  
#include <iostream>  
  
namespace asio = boost::asio;  
  
std::future<std::size_t> SetKey(  
    std::string_view key,  
    std::string_view value,  
    boost::redis::connection& connection,  
    boost::asio::io_context& io_context  
)  
{  
    // clang-format off  
    auto request = std::make_shared<boost::redis::request>();  
    request->push("SET", key, value);  
    auto response = std::make_shared<boost::redis::response<std::string>>();  
    return boost::asio::dispatch(boost::asio::bind_executor(  
        io_context.get_executor(),  
        boost::asio::deferred([&connection, request, response]() {  
            return connection.async_exec(  
                *request,  
                *response,  
                asio::consign(asio::deferred, request, response)  
            );  
        })  
    ))(boost::asio::use_future);  
    // clang-format on  
}  
  
int main()  
{  
    boost::asio::io_context io_context{1};  
    std::thread thread;  
  
    // Set up.  
    boost::redis::connection connection(io_context);  
    boost::redis::config config;  
    connection.async_run(  
        config,  
        {boost::redis::logger::level::warning},  
        [](const boost::system::error_code& error) {  
            if (error)  
            {  
                std::cout << "Redis server setup error: " << error.to_string() << std::endl;  
            }  
        }  
    );  
    thread = std::thread([&io_context] {  
        std::cout << "RedisGlobalCache running" << std::endl;  
        io_context.run();  
        std::cout << "RedisGlobalCache finished" << std::endl;  
    });  
  
    SetKey("key1", "value1", connection, io_context).get();  
  
    asio::post(asio::bind_executor(io_context.get_executor(), [&connection] { connection.cancel(); }));  
    thread.join();  
    return 0;  
}  
```  
  
Explanation:  
- You can think that your `std::thread` "owns" the io_context and any object within it. All Redis communication is going to happen using this dedicated thread. Communication with your main thread is going to use `std::future`, which is thread-safe.  
- `SetKey` returns a `std::future`, which replaces your semaphore. It can communicate values and exceptions back to your code, which you will find useful when implementing the "get" part of the cache. We use `std::future<std::size_t>` because `async_execute` has handler signature `void(error_code, std::size_t)`.  
- Posting and dispatching to the `io_context` from any thread is safe. We need to do it because initiating an `async_execute` in a thread that is not running the context is a race condition.  
- `asio::consign` is a special completion token that is used to keep things alive until an async op finishes. This is telling "please keep request and response alive until `async_execute` completes" (a requirement of Boost.Redis).  
- `asio::deferred` is used here to create an async chain. Read this as the `.then()` function in Javascript: first dispatch to the context, then execute the operation.  
- `asio::use_future` is a special completion token that is used for this kind of patterns. It runs an async operation, and when it finishes, it sets the value of a future instead of calling a callback. Note that, if the operation fails, an exception is thrown (there are ways to prevent this, let me know if you're interested).  
  
Note that `async_run` should technically be wrapped in an `asio::dispatch` call, too.

---

## Comment 4

> Username: bruno-viva  
> Created at: 2024-10-14 16:00:46 UTC  
> Url: https://github.com/boostorg/redis/issues/216#issuecomment-2411670513  

Thanks for the thorough response! It makes sense now. Two quick follow up questions:  
  
- Can the post be simplified to: `asio::post(io_context, [&connection] { connection.cancel(); });` ?  
- Unfortunately the code I am working with does not support exceptions, if you could show a simple example that uses the error codes instead that would be awesome!  
  
Please feel free to close this issue after your response too, I hope this was the right forum to ask these clarifying questions! Thank you

---

## Comment 5

> Username: anarthal  
> Created at: 2024-10-14 16:11:21 UTC  
> Updated at: 2024-10-14 16:11:34 UTC  
> Url: https://github.com/boostorg/redis/issues/216#issuecomment-2411694267  

1. It will boil down to the same thing at the end of the day, but there are some technical differences that you must be aware of. `asio::post(io_context, [&connection] { connection.cancel(); });` first posts to the io_context, then dispatches to the callback's associated executor. This is `asio::system_executor`, for which dispatch is just an inline, immediate completion, so you will observe no difference. However, in other scenarios you might observe differences, so I'd advise to stick to the `bind_executor` syntax. It's longer, but it won't cause you any nasty surprise.  
2. The difference between `post` and `dispatch` is that `dispatch` may call the function you pass inline, while `post` forces always a reschedule. You usually do dispatch when you just want to run a function in a context, and post when you want to force the reschedule to prevent recursion from overflowing your stack. In your case, both will do the same, because you're calling them from outside of the thread running the I/O context. In general, dispatch may be faster than post. dispatch probably captures better what you're trying to do than post.  
3. You probably want to use `asio::as_tuple`. For instance:  
  
```cpp  
  
std::future<std::tuple<error_code, std::size_t>> SetKey(  
    std::string_view key,  
    std::string_view value,  
    boost::redis::connection& connection,  
    boost::asio::io_context& io_context  
)  
{  
    // clang-format off  
    auto request = std::make_shared<boost::redis::request>();  
    request->push("SET", key, value);  
    auto response = std::make_shared<boost::redis::response<std::string>>();  
    return boost::asio::dispatch(boost::asio::bind_executor(  
        io_context.get_executor(),  
        boost::asio::deferred([&connection, request, response]() {  
            return connection.async_exec(  
                *request,  
                *response,  
                asio::consign(asio::as_tuple(asio::deferred), request, response)  
            );  
        })  
    ))(boost::asio::use_future);  
    // clang-format on  
}  
```  
  
BTW I'm not the maintainer of Boost.Redis so I can't close it :)  
  
I think this forum is correct, we also have the [#boost-asio](https://cpplang.slack.com/archives/C06BRML5EFK) channel in the cpplang slack for Asio questions.

---

## Comment 6

> Username: bruno-viva  
> Created at: 2024-10-14 21:30:24 UTC  
> Url: https://github.com/boostorg/redis/issues/216#issuecomment-2412374633  

Thank you!
