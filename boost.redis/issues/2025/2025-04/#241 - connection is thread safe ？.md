# #241 - connection  is  thread safe ？ [Closed]

> Username: nqf  
> Created at: 2025-04-14 05:21:30 UTC  
> Updated at: 2025-04-16 07:42:28 UTC  
> Closed at: 2025-04-16 07:42:27 UTC  
> Url: https://github.com/boostorg/redis/issues/241  

I need to call the set command in multithreading

---

## Comment 1

> Username: criatura2  
> Created at: 2025-04-14 06:50:18 UTC  
> Url: https://github.com/boostorg/redis/issues/241#issuecomment-2800642105  

Hi, no it is not thread safe. Please have a look at this discussion and let me know if it helps your further https://github.com/boostorg/redis/issues/216.

---

## Comment 2

> Username: nqf  
> Created at: 2025-04-14 08:40:31 UTC  
> Url: https://github.com/boostorg/redis/issues/241#issuecomment-2800898414  

Thank you，Is this safe?  
```  
    boost::asio::strand<boost::asio::io_context::executor_type> strand_(  
        io_context1->get_executor());     
  
   boost::asio::co_spawn(  
        strand_,  
        [this]() -> boost::asio::awaitable<void> {  
            co_await conn.set();  
        },  
        boost::asio::detached);  
  
    boost::asio::co_spawn(  
        strand_,  
        [this]() -> boost::asio::awaitable<void> {  
              co_await conn.set();  
        },  
        boost::asio::detached);  
```

---

## Comment 3

> Username: anarthal  
> Created at: 2025-04-14 15:45:12 UTC  
> Url: https://github.com/boostorg/redis/issues/241#issuecomment-2802144878  

I think we're missing context to say whether it's safe or not. I assume `conn.set()`  in this context means calling `redis::connection::async_execute` with a request and response objects such that a `SET` command is executed?  
  
To be safe, all functions you call for a particular connection object must be serialized through the same strand. This includes `async_run`. Hence, this would be safe:  
  
  
```cpp  
  
namespace redis = boost::redis;  
namespace asio = boost::asio;  
  
// The same strand must be used for all operations  
auto strand = asio::make_strand(io_context1);  
  
 // Create the connection using the strand  
redis::connection conn (strand);  
  
// Run the connection through the strand  
asio::co_spawn(strand, conn.async_run(asio::use_awaitable), asio::detached);  
  
// Run any async_execute operation through the strand, too  
asio::co_spawn(strand, [] () -> asio::awaitable<void> {  
     co_await conn.async_execute(/* execute the set */);  
}, asio::detached);  
asio::co_spawn(strand, [] () -> asio::awaitable<void> {  
     co_await conn.async_execute(/* execute the set */);  
}, asio::detached);  
  
```  
  
That said, using strands has a small performance penalty. If you are only using Boost.Redis (as opposed to also Boost.Beast, Boost.MySQL or other Asio operations), running everything through a single strand will serialize everything, neglecting any potential performance benefits from having more than one thread running your io_context.  
  
If your application is multi-threaded (meaning that you want to invoke `SET` operations from any thread), but you don't care about how many threads your io_context has, there are more efficient solutions than running strands. If you can please provide me with more context about your application, I can likely orient you better.  
  
Cheers,  
Ruben.

---

## Comment 4

> Username: nqf  
> Created at: 2025-04-15 00:28:46 UTC  
> Updated at: 2025-04-15 00:33:00 UTC  
> Url: https://github.com/boostorg/redis/issues/241#issuecomment-2803413963  

> I think we're missing context to say whether it's safe or not. I assume `conn.set()` in this context means calling `redis::connection::async_execute` with a request and response objects such that a `SET` command is executed?  
>   
> To be safe, all functions you call for a particular connection object must be serialized through the same strand. This includes `async_run`. Hence, this would be safe:  
>   
> namespace redis = boost::redis;  
> namespace asio = boost::asio;  
>   
> // The same strand must be used for all operations  
> auto strand = asio::make_strand(io_context1);  
>   
>  // Create the connection using the strand  
> redis::connection conn (strand);  
>   
> // Run the connection through the strand  
> asio::co_spawn(strand, conn.async_run(asio::use_awaitable), asio::detached);  
>   
> // Run any async_execute operation through the strand, too  
> asio::co_spawn(strand, [] () -> asio::awaitable<void> {  
>      co_await conn.async_execute(/* execute the set */);  
> }, asio::detached);  
> asio::co_spawn(strand, [] () -> asio::awaitable<void> {  
>      co_await conn.async_execute(/* execute the set */);  
> }, asio::detached);  
> That said, using strands has a small performance penalty. If you are only using Boost.Redis (as opposed to also Boost.Beast, Boost.MySQL or other Asio operations), running everything through a single strand will serialize everything, neglecting any potential performance benefits from having more than one thread running your io_context.  
>   
> If your application is multi-threaded (meaning that you want to invoke `SET` operations from any thread), but you don't care about how many threads your io_context has, there are more efficient solutions than running strands. If you can please provide me with more context about your application, I can likely orient you better.  
>   
> Cheers, Ruben.  
  
Thank you for your answer， I have 4 ways of using it， would you tell me which method is safe?  Based on your answer, I believe Method 1 is absolutely safe  
  
```  
    {  
        // method 1  
        auto io_context = std::make_shared<asio::io_context>();  
        // one thread run io_context  
        std::thread([&] { io_context->run(); }).detach();  
        redis::connection conn (*io_context);  
  
        asio::co_spawn(*io_context, conn.async_run(asio::use_awaitable), asio::detached);  
  
        asio::co_spawn(*io_context, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
        asio::co_spawn(*io_context, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
    }  
  
    {  
        // method 2  
        auto io_context = std::make_shared<asio::io_context>();  
        // 10 thread run io_context  
        for (int i = 0; i < 10; i++)  
            std::thread([&] { io_context->run(); }).detach();  
        redis::connection conn (*io_context);  
  
        asio::co_spawn(*io_context, conn.async_run(asio::use_awaitable), asio::detached);  
  
        asio::co_spawn(*io_context, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
        asio::co_spawn(*io_context, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
    }  
  
    {  
        // method 3  
        auto io_context = std::make_shared<asio::io_context>();  
        // 10 thread run io_context  
        for (int i = 0; i < 10; i++)  
            std::thread([&] { io_context->run(); }).detach();  
        auto strand = asio::make_strand(*io_context);  
  
        redis::connection conn (strand);  
  
        asio::co_spawn(strand, conn.async_run(asio::use_awaitable), asio::detached);  
  
        asio::co_spawn(strand, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
        asio::co_spawn(strand, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
    }  
  
    {  
        // method 4  
        auto io_context = std::make_shared<asio::io_context>();  
        std::thread([&] { io_context->run(); }).detach();  
  
        auto io_context2 = std::make_shared<asio::io_context>();  
        std::thread([&] { io_context2->run(); }).detach();  
  
        redis::connection conn (*io_context);  
  
        asio::co_spawn(*io_context, conn.async_run(asio::use_awaitable), asio::detached);  
  
        asio::co_spawn(*io_context, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
        asio::co_spawn(*io_context2, [] () -> asio::awaitable<void> {  
            co_await conn.async_execute(/* execute the set */);  
        }, asio::detached);  
    }  
```

---

## Comment 5

> Username: anarthal  
> Created at: 2025-04-15 14:39:30 UTC  
> Url: https://github.com/boostorg/redis/issues/241#issuecomment-2805555638  

* method 1 is safe  
* method 2 is a race condition  
* method 3 is safe  
* method 4 is a race condition (when you `co_spawn` in the 2nd `io_context`, the code runs in the second thread. You're then initiating an `async_execute` from that thread while the thread in the 1st `io_context` calls run)  
  
Some notes:  
  
```cpp  
// This  
auto io_context = std::make_shared<asio::io_context>();  
// 10 thread run io_context  
for (int i = 0; i < 10; i++)  
    std::thread([&] { io_context->run(); }).detach();  
  
// Can be rewritten shorter and probably more efficient as  
asio::thread_pool ctx (10); // like an io_context, but with 10 threads running it  
```  
  
I recommend running your code with the `-fsanitize=thread` flag, which can catch some of these things, just to verify that you got everything right. I've been working with Asio for some time and I always find surprising things.

---

## Comment 6

> Username: nqf  
> Created at: 2025-04-16 02:45:15 UTC  
> Url: https://github.com/boostorg/redis/issues/241#issuecomment-2808079601  

Thank you very much for your answer， Actually, I am running an io_context pool locally, One thread per context，if use  `method 1` （Only use one io_context and one redis connection）， I'm not sure if its efficiency is sufficient  
  
```  
std::vector<io_context> io_context_pool  
for (int i =0 ;i < 10; i++) {  
   std::thread([&] { io_context->run(); }).detach();  
   io_context_pool.push_back(io_context);  
}  
```

---

## Comment 7

> Username: anarthal  
> Created at: 2025-04-16 07:05:22 UTC  
> Updated at: 2025-04-16 07:06:21 UTC  
> Url: https://github.com/boostorg/redis/issues/241#issuecomment-2808599021  

Bear in mind that if you do method 3, and all the code run by the `io_context` threads is guarded by the same strand, then your code is going to be less efficient than with just once thread. Think of a strand like Asio's equivalent to `std::mutex`. If all your code is guarded by a single `std::mutex`, and you add more threads, you won't be any faster.  
  
For method 3 to yield better results, parts of your I/O handling code need to be outside of the Redis strand. I don't know your application, so let's say you're also doing HTTP in addition to Redis. If your code looks like this, method 3 may help:  
  
```cpp  
asio::co_await(io_ctx1, [&] () -> asio::awaitable<void> {  
    // Read an HTTP request. This happens outside of the Redis' strand  
    co_await http::async_read(/* */);  
   
    // Now run the set command. This needs to be done within the connection's strand.  
    // You can co_await coroutines that you co_spawn. Using co_spawn instead of plain  
    // co_await allows you to change the executor where the coroutine will run  
    co_await asio::co_spawn(strand, []() -> asio::awaitable<void> {  
          co_await conn.async_execute( /* ... */);  
    });  
  
    // Write your response back  
    co_await http::async_write(/* ... */);  
}, asio::detached);  
```  
  
Note that, depending on what you're doing in the HTTP section, you might need another strand per HTTP connection.  
  
However, if your code looks only like this, method 3 won't help:  
  
```cpp  
asio::co_spawn(strand, []() -> asio::awaitable<void> {  
      co_await conn.async_execute( /* ... */);  
});  
```  
  
You might also want to try creating more than one connection to Redis. I don't know if this will increase efficiency or not, though.  
  
In any case, you need to measure your throughput before making a decision.
