# #402 - Document interaction between asio::cancel_after and pool_params::thread_safe [Closed]

> Username: bruno-viva  
> Created at: 2025-01-23 16:35:15 UTC  
> Updated at: 2025-02-24 21:11:37 UTC  
> Closed at: 2025-02-24 21:11:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/402  

Hi!  
  
I think I may have found a deadlock that I boiled down to `cancel_after`. Here is the minimal code:  
  
```cpp  
#include <boost/asio/io_context.hpp>  
#include <boost/mysql/connection_pool.hpp>  
#include <iostream>  
#include <thread>  
  
std::mutex mutex;  
int counter = 0;  
  
void GetConnection(boost::mysql::connection_pool& pool, int index) {  
  pool.async_get_connection(boost::asio::cancel_after(  
      std::chrono::seconds(6),  
      [index](boost::mysql::error_code error, boost::mysql::pooled_connection /*connection*/) {  
        if (error) {  
          std::cout << error.what() << std::endl;  
          return;  
        }  
        std::cout << index << std::endl;  
        const std::lock_guard<std::mutex> lock(mutex);  
        --counter;  
      }));  
}  
  
boost::mysql::pool_params GetParams() {  
  boost::mysql::pool_params params;  
  params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
  params.username = "root";  
  params.password = "root";  
  params.database = "test_0";  
  params.max_size = 4;  
  params.initial_size = 4;  
  params.thread_safe = true;  
  params.multi_queries = true;  
  params.connect_timeout = std::chrono::seconds(2);  
  params.retry_interval = std::chrono::seconds(1);  
  return params;  
}  
  
int main() {  
  for (int retries = 0; retries < 100; ++retries) {  
    std::cout << "Retry: " << retries << std::endl;  
  
    boost::asio::io_context io_context;  
    boost::mysql::connection_pool pool(io_context, GetParams());  
    pool.async_run(boost::asio::detached);  
  
    std::vector<std::thread> threads;  
    for (int i = 0; i < 4; i++) {  
      threads.emplace_back([&io_context]() { io_context.run(); });  
    }  
  
    // Must get 2 connections. With just 1 connection it won't deadlock.  
    counter = 2;  
    for (int i = 0; i < 2; ++i) {  
      GetConnection(pool, i);  
    }  
  
    while (true) {  
      const std::lock_guard<std::mutex> lock(mutex);  
      if (counter == 0) {  
        break;  
      }  
    }  
  
    io_context.stop();  
  
    for (auto& thread : threads) {  
      thread.join();  
    }  
  }  
}  
```  
  
Compiled with: `$ clang++ -std=gnu++17 -stdlib=libc++ test.cpp -lboost_thread -lboost_charconv -lssl -lcrypto -g`  
  
The output is something like:  
  
```  
$ ./a.out   
Retry: 0  
0  
1  
Retry: 1  
0  
1  
...  
Retry: 8  
0  
1  
Retry: 9  
0  
<it gets stuck here>  
```  
  
When I remove the `cancel_after`, it never gets stuck, and it works as expected (but no deadline is set).  
  
Am I misusing the `cancel_after`? Or am I doing something unexpected to cause this deadlock?  
  
Thanks in advance

---

## Comment 1

> Username: anarthal  
> Created at: 2025-01-23 16:58:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2610423167  

Hi!  
  
From the top of my head, I don't see anything evidently wrong, so it might be an actual bug. I will look into this tomorrow.  
  
In the meanwhile, just to discard potential problems, would you mind trying the following?  
* Replace the mutex around your counter by an std::atomic_int. Just in case there's some inline callback invocation we're missing. In general, try to avoid mutexes in callbacks.  
* Replace your io_context + vector of threads by an asio::thread_pool. Just in case.  
  
Many thanks,  
Ruben.

---

## Comment 2

> Username: bruno-viva  
> Created at: 2025-01-23 21:43:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2611078732  

Thanks for replying!  
  
With atomic:  
  
```cpp  
#include <boost/asio/io_context.hpp>  
#include <boost/mysql/connection_pool.hpp>  
#include <iostream>  
#include <thread>  
  
std::atomic<int> counter = 0;  
  
void GetConnection(boost::mysql::connection_pool& pool, int index) {  
  pool.async_get_connection(boost::asio::cancel_after(  
      std::chrono::seconds(6),  
      [index](boost::mysql::error_code error, boost::mysql::pooled_connection /*connection*/) {  
        if (error) {  
          std::cout << error.what() << std::endl;  
          return;  
        }  
        std::cout << index << std::endl;  
        --counter;  
      }));  
}  
  
boost::mysql::pool_params GetParams() {  
  boost::mysql::pool_params params;  
  params.server_address.emplace_host_and_port("127.0.0.1", 57761);  
  params.username = "root";  
  params.password = "root";  
  params.database = "test_0";  
  params.max_size = 4;  
  params.initial_size = 4;  
  params.thread_safe = true;  
  params.multi_queries = true;  
  params.connect_timeout = std::chrono::seconds(2);  
  params.retry_interval = std::chrono::seconds(1);  
  return params;  
}  
  
int main() {  
  for (int retries = 0; retries < 100; ++retries) {  
    std::cout << "Retry: " << retries << std::endl;  
  
    boost::asio::io_context io_context;  
    boost::mysql::connection_pool pool(io_context, GetParams());  
    pool.async_run(boost::asio::detached);  
  
    std::vector<std::thread> threads;  
    for (int i = 0; i < 4; i++) {  
      threads.emplace_back([&io_context]() { io_context.run(); });  
    }  
  
    // Must get 2 connections. With just 1 connection it won't deadlock.  
    counter = 2;  
    for (int i = 0; i < 2; ++i) {  
      GetConnection(pool, i);  
    }  
  
    while (true) {  
      if (counter == 0) {  
        break;  
      }  
    }  
  
    io_context.stop();  
  
    for (auto& thread : threads) {  
      thread.join();  
    }  
  }  
}  
```  
  
With asio::thread_pool:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/mysql/connection_pool.hpp>  
#include <iostream>  
#include <thread>  
  
std::atomic<int> counter = 0;  
  
void GetConnection(boost::mysql::connection_pool& pool, int index) {  
  pool.async_get_connection(boost::asio::cancel_after(  
      std::chrono::seconds(6),  
      [index](boost::mysql::error_code error, boost::mysql::pooled_connection /*connection*/) {  
        if (error) {  
          std::cout << error.what() << std::endl;  
          return;  
        }  
        std::cout << index << std::endl;  
        --counter;  
      }));  
}  
  
boost::mysql::pool_params GetParams() {  
  boost::mysql::pool_params params;  
  params.server_address.emplace_host_and_port("127.0.0.1", 57761);  
  params.username = "root";  
  params.password = "root";  
  params.database = "test_0";  
  params.max_size = 4;  
  params.initial_size = 4;  
  params.thread_safe = true;  
  params.multi_queries = true;  
  params.connect_timeout = std::chrono::seconds(2);  
  params.retry_interval = std::chrono::seconds(1);  
  return params;  
}  
  
int main() {  
  for (int retries = 0; retries < 100; ++retries) {  
    std::cout << "Retry: " << retries << std::endl;  
  
    boost::asio::thread_pool thread_pool(4);  
    boost::mysql::connection_pool pool(thread_pool, GetParams());  
    pool.async_run(boost::asio::detached);  
  
    // Must get 2 connections. With just 1 connection it won't deadlock.  
    counter = 2;  
    for (int i = 0; i < 2; ++i) {  
      GetConnection(pool, i);  
    }  
  
    while (true) {  
      if (counter == 0) {  
        break;  
      }  
    }  
  
    thread_pool.stop();  
    thread_pool.join();  
  }  
}  
```  
  
Same deadlock happens with both versions.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-01-24 11:36:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2612312510  

After running and inspecting the code, there's a race condition with `cancel_after`. Under the hood, `cancel_after` creates a timer and runs it in parallel with `async_get_connection`. This means that at certain points in time, there might be two concurrent handlers sharing state without a strand to protect them, yielding a race condition. For example:  
  
* The thread calling `GetConnection` (let's call it T1) initiates `async_get_connection`. Because the connection pool is configured with thread-safety enabled, there's an internal dispatch to a strand.  
* T1 starts the timer associated to `boost::asio::cancel_after`.  
* In the meanwhile, another thread (T2) sees that there's an available connection, causing `async_get_connection` to complete. As part of the completion, the timer gets cancelled. (In reality, there's another rescheduling here, but it's irrelevant for our discussion).  
  
The scenario above modifies the timer concurrently from both T1 and T2, causing undefined behavior.  
  
The correct way to handle this is by using a `boost::asio::strand` object (full docs [here](https://live.boost.org/doc/libs/1_87_0/doc/html/boost_asio/overview/core/strands.html)). For example, using the last code sample:  
  
```cpp  
void GetConnection(boost::mysql::connection_pool& pool, int index)  
{  
    // Create a strand for the task  
    auto s = boost::asio::make_strand(pool.get_executor());  
  
    // Run the initiation within the strand  
    boost::asio::dispatch(boost::asio::bind_executor(s, [&pool, s, index]() {  
        pool.async_get_connection(boost::asio::cancel_after(  
            std::chrono::seconds(6),  
            boost::asio::bind_executor(  
                s,  
                [index](boost::mysql::error_code error, boost::mysql::pooled_connection /*connection*/) {  
                    if (error)  
                    {  
                        std::cout << error.what() << std::endl;  
                        return;  
                    }  
                    std::cout << index << std::endl;  
                    --counter;  
                }  
            )  
        ));  
    }));  
}  
```  
  
Could you please test it and verify whether it works for you?  
  
Some points in the code above:  
* The `dispatch` ensures that initiation code (everything before the first callback) runs under protection.  
* The inner `bind_executor` ensures that all internal callbacks (i.e. the one issued after a connection is acquired, and the ones associated to the timer) run under protection.  
  
Unfortunately, the timer in `cancel_after` is outside of the `thread_pool`'s control, so there's no way to prevent the race condition in library code, even when setting `thread_safe=true`. `thread_safe=true` only protects the pool's internal state, and the timer escapes this. If your program has some notion of "sessions" or "client connections", you usually create one strand per session. `thread_safe=true` avoid having to do black magic to switch from this per-session strand to a global strand and back.  
  
I hope this helps.  
  
Regards,  
Ruben.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-01-24 11:38:18 UTC  
> Updated at: 2025-01-24 11:38:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2612315886  

Also, note that all the machinery required to support multi-threading comes at a performance cost. You might consider alternatives, like running an `io_context` per thread, or running a single `io_context` and dispatching heavyweight work to a separate thread pool to avoid blocking the I/O thread. In any case, don't assume anything about performance, measure it first. And a warning label: multi-threading in Asio is very hard to get right.

---

## Comment 5

> Username: bruno-viva  
> Created at: 2025-01-24 17:26:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2613041900  

Thank you so much! It worked, I can't reproduce a deadlock now. What is odd is that I compiled it with -fsanitize=thread and it didn't accuse any warnings. I think this behavior should be documented in the examples, so that more people won't fall into this pitfall like me!  
  
> Also, note that all the machinery required to support multi-threading comes at a performance cost. You might consider alternatives, like running an io_context per thread, or running a single io_context and dispatching heavyweight work to a separate thread pool to avoid blocking the I/O thread. In any case, don't assume anything about performance, measure it first. And a warning label: multi-threading in Asio is very hard to get right.  
  
By having a single thread per io_context, I can only have one thread per pool? I think for my use-case, the database itself will be the bottleneck (write/read QPS), but I will give it a try.  
  
Thanks again for your help!

---

## Comment 6

> Username: bruno-viva  
> Created at: 2025-01-24 18:52:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2613182731  

To add to the above, when using the connection with async_execute, I was able to reproduce the deadlock again:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/mysql/connection_pool.hpp>  
#include <iostream>  
#include <thread>  
  
std::atomic<int> counter = 0;  
  
void GetConnection(boost::mysql::connection_pool& pool, int index) {  
  auto strand = boost::asio::make_strand(pool.get_executor());  
  boost::asio::dispatch(strand, [&pool, index, strand]() mutable {  
    pool.async_get_connection(boost::asio::cancel_after(  
        std::chrono::seconds(6),  
        boost::asio::bind_executor(  
            strand, [index, strand](boost::mysql::error_code error,  
                                    boost::mysql::pooled_connection connection) mutable {  
              if (error) {  
                std::cout << error.what() << std::endl;  
                return;  
              }  
              // Don't really need to dispatch here since this executes in the strand,  
              // but just in case..  
              boost::asio::dispatch(strand, [connection = std::move(connection), strand,  
                                             index]() mutable {  
                auto results = std::make_unique<boost::mysql::results>();  
                connection->async_execute(  
                    "SELECT 1", *results,  
                    boost::asio::bind_executor(  
                        strand, boost::asio::cancel_after(  
                                    std::chrono::seconds(6), [results = std::move(results), index,  
                                                              connection = std::move(connection)](  
                                                                 boost::mysql::error_code error) {  
                                      if (error) {  
                                        std::cout << error.what() << std::endl;  
                                        return;  
                                      }  
                                      std::cout << "Got result " << index << std::endl;  
                                      counter--;  
                                    })));  
              });  
            })));  
  });  
}  
  
boost::mysql::pool_params GetParams() {  
  boost::mysql::pool_params params;  
  params.server_address.emplace_host_and_port("127.0.0.1", 57761);  
  params.username = "root";  
  params.password = "root";  
  params.database = "test_0";  
  params.max_size = 4;  
  params.initial_size = 4;  
  params.thread_safe = true;  
  params.multi_queries = true;  
  params.connect_timeout = std::chrono::seconds(2);  
  params.retry_interval = std::chrono::seconds(1);  
  return params;  
}  
  
int main() {  
  for (int retries = 0; retries < 100; ++retries) {  
    std::cout << "Retry: " << retries << std::endl;  
  
    boost::asio::thread_pool thread_pool(4);  
    boost::mysql::connection_pool pool(thread_pool, GetParams());  
    pool.async_run(boost::asio::detached);  
  
    // Must get 2 connections. With just 1 connection it won't deadlock.  
    counter = 2;  
    for (int i = 0; i < 2; ++i) {  
      GetConnection(pool, i);  
    }  
  
    while (true) {  
      if (counter == 0) {  
        break;  
      }  
    }  
  
    thread_pool.stop();  
    thread_pool.join();  
  }  
}  
```  
  
I am dispatching everything to the strand. Am I missing something? Thanks in advance

---

## Comment 7

> Username: anarthal  
> Created at: 2025-01-24 19:17:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2613221065  

Yup, you got the order of bind_executor/cancel_after wrong in async_execute. This is how it should be:  
  
```cpp  
                        connection->async_execute(  
                                "SELECT 1",  
                                *results,  
                                boost::asio::cancel_after(  
                                    std::chrono::seconds(6),  
                                    boost::asio::bind_executor(  
                                        strand,  
                                        [results = std::move(results),  
                                         index,  
                                         connection = std::move(connection)](boost::mysql::error_code error) {  
                                            if (error)  
                                            {  
                                                std::cout << error.what() << std::endl;  
                                                return;  
                                            }  
                                            std::printf("Got result %d\n", index);  
                                            counter--;  
                                        }  
                                    )  
                                )  
                            );  
```  
  
By doing this, cancel_after sees the bound executor and uses it. Could you please run the code with this change and confirm?  
  
Thanks,  
Ruben.

---

## Comment 8

> Username: bruno-viva  
> Created at: 2025-01-24 21:35:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2613417834  

Dang, thanks for pointing that out! Still wrapping my head around asio and completion tokens :D  
  
Would this also be an issue if I were using coroutines?  
  
This is the final version that works (IIUC connection->async_execute doesn't need dispatching since the handler is being executed within the strand):  
  
```cpp  
void GetConnection(boost::mysql::connection_pool& pool, int index) {  
  auto strand = boost::asio::make_strand(pool.get_executor());  
  boost::asio::dispatch(strand, [&pool, index, strand]() mutable {  
    pool.async_get_connection(boost::asio::cancel_after(  
        std::chrono::seconds(6),  
        boost::asio::bind_executor(strand, [index, strand](  
                                               boost::mysql::error_code error,  
                                               boost::mysql::pooled_connection connection) mutable {  
          if (error) {  
            std::cout << error.what() << std::endl;  
            return;  
          }  
          auto results = std::make_unique<boost::mysql::results>();  
          connection->async_execute(  
              "SELECT 1", *results,  
              boost::asio::cancel_after(  
                  std::chrono::seconds(6),  
                  boost::asio::bind_executor(  
                      strand, [results = std::move(results), index,  
                               connection = std::move(connection)](boost::mysql::error_code error) {  
                        if (error) {  
                          std::cout << error.what() << std::endl;  
                          return;  
                        }  
                        std::cout << "Got result " << index << std::endl;  
                        counter--;  
                      })));  
        })));  
  });  
}  
```  
  
Thank you!!! (please feel free to close this issue)

---

## Comment 9

> Username: anarthal  
> Created at: 2025-01-26 16:50:40 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2614502468  

If you spawn your coroutine on a strand, all operations will run as-if bound to that strand. So this should be safe:  
  
```cpp  
asio::awaitable<void> GetConnection(boost::mysql::connection_pool& pool, int index) {  
  // We're already running in the strand here  
  auto conn = co_await pool.async_get_connection(asio::cancel_after(6s));  
  // Check errors  
  mysql::results r;  
  co_await conn->async_execute("SELECT 1", r);  
  // Check errors  
}  
```  
  
As long as you pass a strand to `co_spawn`.  
  
I'll leave this open to document what you found, since I agree it's counter-intuitive behavior.

---

## Comment 10

> Username: anarthal  
> Created at: 2025-01-26 16:50:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/402#issuecomment-2614502574  

Should you need any more help, let me know.
