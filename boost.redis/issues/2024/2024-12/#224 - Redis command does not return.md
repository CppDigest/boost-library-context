# #224 - Redis command does not return [Closed]

> Username: nestal  
> Created at: 2024-12-18 15:53:31 UTC  
> Updated at: 2024-12-20 12:21:25 UTC  
> Closed at: 2024-12-20 12:21:24 UTC  
> Url: https://github.com/boostorg/redis/issues/224  

```c++  
	auto redis = std::make_shared<boost::redis::connection>(co_await boost::asio::this_coro::executor);  
	redis->async_run(redis_cfg, {}, consign(boost::asio::detached, redis));  
  
	std::cerr << "Before ping\n";  
	boost::redis::request ping;  
	ping.push("PING", "hello");  
	co_await redis->async_exec(ping, boost::redis::ignore, boost::asio::deferred);  
	std::cerr << "After ping\n";  
```   
For some reason, the message "After ping" will never be printed. It happens randomly after the above block has been running for a couple of times.  
  
The test program is copied from the boost beast awaitable server example. It just open a redis connection and run a "PING" command every time a HTTP client connects. After serving a number of HTTP requests, the redis command never returns.  
  
The problem can be reproduced by the following code building with boost 1.87. A simple `watch curl http://localhost:8080` can cause the server to reproduce it.  
  
```c++  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/algorithm/hex.hpp>  
#include <boost/redis/src.hpp>  
  
#include <cstdlib>  
#include <iostream>  
#include <memory>  
#include <thread>  
  
namespace http = boost::beast::http;  
  
// Handles an HTTP server connection  
boost::asio::awaitable<void> do_session(boost::beast::tcp_stream stream)  
{  
    // This buffer is required to persist across reads  
    boost::beast::flat_buffer buffer;  
  
	boost::redis::config redis_cfg;  
	redis_cfg.addr.host = "127.0.0.1";  
	redis_cfg.addr.port = "6379";  
  
	auto redis = std::make_shared<boost::redis::connection>(co_await boost::asio::this_coro::executor);  
	redis->async_run(redis_cfg, {}, consign(boost::asio::detached, redis));  
  
	std::cerr << "Before ping\n";  
	boost::redis::request ping;  
	ping.push("PING", "hello");  
	co_await redis->async_exec(ping, boost::redis::ignore, boost::asio::deferred);  
	std::cerr << "After ping\n";  
  
	std::shared_ptr<void> closer{redis.get(), [redis](auto*){redis->cancel();}};  
  
	while (true)  
    {  
        // Set the timeout.  
        stream.expires_after(std::chrono::seconds(30));  
  
        // Read a request  
        http::request<http::string_body> req;  
        co_await http::async_read(stream, buffer, req);  
  
        // Handle the request  
		http::response<http::empty_body> resp{http::status::ok, req.version()};  
		resp.prepare_payload();  
        http::message_generator msg = std::move(resp);//co_await azuma.handle_request(std::move(req));  
  
        // Determine if we should close the connection  
        const bool keep_alive = msg.keep_alive();  
  
        // Send the response  
        co_await async_write(stream, std::move(msg));  
  
        if (!keep_alive)  
        {  
            // This means we should close the connection, usually because  
            // the response indicated the "Connection: close" semantic.  
            break;  
        }  
    }  
  
    // Send a TCP shutdown  
    stream.socket().shutdown(boost::asio::ip::tcp::socket::shutdown_send);  
  
    // At this point the connection is closed gracefully  
    // we ignore the error because the client might have  
    // dropped the connection already.  
}  
  
// Accepts incoming connections and launches the sessions  
boost::asio::awaitable<void> do_listen()  
{  
    auto executor = co_await boost::asio::this_coro::executor;  
    auto acceptor = boost::asio::ip::tcp::acceptor{executor, boost::asio::ip::tcp::endpoint{boost::asio::ip::make_address("0.0.0.0"), 8080}};  
  
    while (true)  
    {  
        co_spawn(  
            executor,  
            do_session(  
                boost::beast::tcp_stream{co_await acceptor.async_accept()}  
            ),  
            [](std::exception_ptr p)  
            {  
                if(p)  
                {  
                    try  
                    {  
                        std::rethrow_exception(p);  
                    }  
                    catch(const std::exception& e)  
                    {  
                    	std::cerr << e.what() << '\n';  
                    }  
                }  
            });  
    }  
}  
  
int main(int argc, char** argv)  
{  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc{5};  
  
	// Spawn a listening port  
	co_spawn(  
		ioc,  
		do_listen(),  
		[](std::exception_ptr ex)  
		{  
			if(ex)  
			{  
				try  
				{  
					std::rethrow_exception(ex);  
				}  
				catch(std::exception const& e)  
				{  
					std::cerr << "Error: " << e.what() << std::endl;  
				}  
			}  
		});  
  
	// Run the I/O service on the requested number of threads  
	std::vector<std::thread> v;  
	v.reserve(4);  
	for(auto i = 4; i > 0; --i)  
		v.emplace_back([&ioc] { ioc.run(); });  
	ioc.run();  
	return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: nestal  
> Created at: 2024-12-18 15:55:03 UTC  
> Url: https://github.com/boostorg/redis/issues/224#issuecomment-2551694144  

This CMake file is used to build the test program. I used Debian bookworm which ships GCC 12.2.0.  
  
```  
cmake_minimum_required(VERSION 3.25)  
project(coro VERSION 0.0.1)  
  
set(CMAKE_CXX_STANDARD 23)  
  
set(BOOST_INCLUDE_LIBRARIES asio beast redis endian system program_options)  
set(BOOST_ENABLE_CMAKE ON)  
  
include(FetchContent)  
FetchContent_Declare(  
	Boost  
	GIT_REPOSITORY https://github.com/boostorg/boost.git  
	GIT_TAG boost-1.87.0  
	GIT_SHALLOW TRUE  
)  
FetchContent_MakeAvailable(Boost)  
  
find_package(Threads REQUIRED)  
  
add_executable(coro main.cpp)  
target_link_libraries(coro PUBLIC Boost::asio Boost::beast Boost::redis)  
```

---

## Comment 2

> Username: criatura2  
> Created at: 2024-12-19 08:40:09 UTC  
> Url: https://github.com/boostorg/redis/issues/224#issuecomment-2553086066  

Hi, you don't need more than once redis connection in your app (usually), so instead of creating one in each http session, `do_session` above, you should create a single redis connection in `do_listen` and pass it as parameter to all `do_session` that you create.  
  
Also, the code above has data races because redis connections are not thread safe. I advise you to test your code with one thread first to make sure that it is correct. Then, if more threads are really needed you will have to spawn the redis connection into a strand, see `make_strand` in the Asio documentation.

---

## Comment 3

> Username: nestal  
> Created at: 2024-12-19 10:15:06 UTC  
> Url: https://github.com/boostorg/redis/issues/224#issuecomment-2553325184  

If redis connections are not thread safe, how can a multithreaded application to use a single redis connection without races? How exactly to "spawn the redis connection into a strand"? Do you mean calling co_spawn() with a strand as executor?

---

## Comment 4

> Username: anarthal  
> Created at: 2024-12-19 16:53:00 UTC  
> Url: https://github.com/boostorg/redis/issues/224#issuecomment-2555071834  

I'd follow this model:  
  
* I'd create just one `redis::connection` per application. Connections can safely multiplex concurrent requests from several sessions, and are relatively heavyweight. You should only need one of them per application. Think of a connection like a "global service" that your individual connections may access.  
* Following the above, your single `redis::connection` will be protected by a strand. All code that might imply reads or writes to the connection must execute within the connection's strand.  
* Each individual HTTP session should also get its own strand. These strands must be different to the one protecting the global `redis::connection`. Otherwise, the global strand will serialize everything, neglecting any possible benefit that multi-threading may grant you.  
  
I've re-written the HTTP example to follow this model. More explanations on how the code works at the end of this post (it's a little bit long).  
  
I recommend to run some tests under `-fsanitize=thread` to verify that everything's correct.  
  
```cpp  
#include <boost/asio/any_io_executor.hpp>  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/deferred.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/this_coro.hpp>  
#include <boost/asio/thread_pool.hpp>  
#include <boost/asio/use_awaitable.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/redis/connection.hpp>  
#include <boost/redis/ignore.hpp>  
#include <boost/redis/request.hpp>  
#include <boost/redis/src.hpp>  
  
#include <chrono>  
#include <cstdlib>  
#include <iostream>  
#include <utility>  
  
namespace beast = boost::beast;  
namespace http = boost::beast::http;  
namespace asio = boost::asio;  
namespace redis = boost::redis;  
  
// Executes a Redis request within the redis::connection's strand  
template <class Response>  
asio::awaitable<void> safe_exec(redis::connection& conn, const redis::request& req, Response& res)  
{  
    // Enter the Redis connection's strand.  
    // This is required because async_exec() will modify the connection's state before  
    // issuing any child async operations.  
    co_await asio::dispatch(asio::bind_executor(conn.get_executor(), asio::deferred));  
  
    // We're now running in the connection's strand. You can remove this check.  
    // Execute the request now that we're running in the connection's strand.  
    // Intermediate completions run by async_exec will run within the connection's strand  
    // because the coroutine is now running within the connection's strand.  
    // When co_await returns, we're still in the connection's strand.  
    co_await conn.async_exec(req, res, asio::bind_executor(conn.get_executor(), asio::deferred));  
  
    // We're still running in the connection's strand  
    // Exit the connection's strand and enter back into the current session's strand  
    co_await asio::post(asio::deferred);  
  
    // We're no longer running in the connection's strand, and we're back to the coroutine's strand  
}  
  
// Handles an HTTP server connection  
asio::awaitable<void> do_session(boost::beast::tcp_stream stream, redis::connection& conn)  
{  
    // This buffer is required to persist across reads  
    beast::flat_buffer buffer;  
  
    while (true)  
    {  
        // Set the timeout.  
        stream.expires_after(std::chrono::seconds(30));  
  
        // Read a request  
        http::request<http::string_body> req;  
        co_await http::async_read(stream, buffer, req);  
  
        // Handle the request by querying Redis. Insert your code here  
        redis::request ping;  
        ping.push("incr", "num-requests");  
        co_await safe_exec(conn, ping, redis::ignore);  
  
        // Handle the request  
        http::response<http::empty_body> resp{http::status::ok, req.version()};  
        resp.prepare_payload();  
        http::message_generator msg = std::move(resp);  // co_await azuma.handle_request(std::move(req));  
  
        // Determine if we should close the connection  
        const bool keep_alive = msg.keep_alive();  
  
        // Send the response  
        co_await async_write(stream, std::move(msg));  
  
        if (!keep_alive)  
        {  
            // This means we should close the connection, usually because  
            // the response indicated the "Connection: close" semantic.  
            break;  
        }  
    }  
  
    // Send a TCP shutdown  
    stream.socket().shutdown(asio::ip::tcp::socket::shutdown_send);  
  
    // At this point the connection is closed gracefully  
    // we ignore the error because the client might have  
    // dropped the connection already.  
}  
  
// Accepts incoming connections and launches the sessions  
asio::awaitable<void> do_listen(redis::connection& conn)  
{  
    auto executor = co_await asio::this_coro::executor;  
    auto acceptor = asio::ip::tcp::acceptor{  
        executor,  
        asio::ip::tcp::endpoint{asio::ip::make_address("0.0.0.0"), 8080}  
    };  
  
    while (true)  
    {  
        // Listen for a new connection  
        auto sock = co_await acceptor.async_accept();  
  
        // Launch a new coroutine for the new session.  
        // Each session gets its own strand  
        asio::co_spawn(  
            asio::make_strand(executor),  
            do_session(beast::tcp_stream{std::move(sock)}, conn),  
            [](std::exception_ptr p) {  
                if (p)  
                {  
                    try  
                    {  
                        std::rethrow_exception(p);  
                    }  
                    catch (const std::exception& e)  
                    {  
                        std::cerr << e.what() << '\n';  
                    }  
                }  
            }  
        );  
    }  
}  
  
int main()  
{  
    // An execution context, consisting of 5 threads (plus the main thread => 6 threads)  
    asio::thread_pool ctx{1};  
  
    // Redis connection. We create a single connection,  
    // and a single strand for the connection.  
    // Because we constructed the connection from the strand,  
    // all intermediate handlers executed by async_run will also use  
    // the strand. We use dispatch so the initial async_run code also happens  
    // within the connection's strand.  
    auto redis_strand = asio::make_strand(ctx);  
    redis::connection conn(redis_strand);  
    asio::dispatch(redis_strand, [&conn] {  
        redis::config redis_cfg;  
        redis_cfg.addr.host = "127.0.0.1";  
        redis_cfg.addr.port = "6379";  
        redis_cfg.health_check_interval = std::chrono::seconds(0);  
        conn.async_run(std::move(redis_cfg), {}, asio::detached);  
    });  
  
    // Spawn a listening port  
    asio::co_spawn(ctx, do_listen(conn), [](std::exception_ptr ex) {  
        if (ex)  
        {  
            try  
            {  
                std::rethrow_exception(ex);  
            }  
            catch (std::exception const& e)  
            {  
                std::cerr << "Error: " << e.what() << std::endl;  
            }  
        }  
    });  
  
    // Attach the main thread to the context, and run forever  
    ctx.attach();  
    return EXIT_SUCCESS;  
}  
```  
  
Some rules on how Asio handles things:  
  
* Async operations like `redis::connection::async_exec` are called composed operations, because they're made up of other async operations. Think of `async_exec` as a chained series of socket reads and writes.  
* When one of these intermediate operation finishes, a callback runs. This is called an intermediate handlers. Such callbacks are provided by Boost.Redis, so you don't get to see them. These callbacks modify the connection state, and must run through a strand.  
* When the last intermediate handler finishes, the coroutine is resumed, effectively making `co_await` return. Underneath, everything is callbacks, but it's hidden away from you.  
* A strand protects objects by allowing handlers to be run within the strand. A strand is thus an executor. You can safely post handlers from different threads to a strand, and it will make them run serially (one handler at a time). For our program to be safe, we must ensure that no two handlers modify shared state without running through a strand.  
* I/O objects, like `redis::connection`, have an associated executor, that might be a strand.  
* When we initiate a composed async operation on an object that was constructed from a strand, all intermediate handlers use the strand, unless if there's a co_await in front of it. So `conn.async_run(std::move(redis_cfg), {}, asio::detached);` makes all handlers run through the strand.  
* When invoking an async function like `async_run`, some code runs before the first handler. This code is *not* protected by the strand automatically, so we need to protect it ourselves. We do this by using `asio::dispatch` or `asio::post`, which essentially mean "please run me this code through this executor".  
* When a coroutine is created using `co_spawn`, you supply an executor (that may be a strand). This executor becomes the coroutine's associated executor. You ca retrieve it calling `co_await asio::this_coro::executor`.  
* When you `co_await` an async operation, intermediate handlers will use the coroutine's associated executor by default (and not the executor you used to construct the I/O object), unless you use `asio::bind_executor`. This means that if we had written `co_await conn.async_exec(req, res, asio::deferred);` directly inside `do_session`, `async_exec`'s handlers would have run within the session's strand, creating a race condition.  
* `safe_exec` uses `asio::dispatch` to enter the connection's strand, then uses `bind_executor` to ensure that all handlers in `async_exec` run within the connection's strand. Finally, it uses `asio::post` to exit the connection's strand.  
  
Hope this helps.

---

## Comment 5

> Username: nestal  
> Created at: 2024-12-20 10:52:53 UTC  
> Url: https://github.com/boostorg/redis/issues/224#issuecomment-2556761096  

Thank you for your detailed explanation, @anarthal . I do have one question: given that the example server call `co_spawn()` for each TCP session it accept, does it still require a strand for each session?

---

## Comment 6

> Username: anarthal  
> Created at: 2024-12-20 11:12:23 UTC  
> Url: https://github.com/boostorg/redis/issues/224#issuecomment-2556793121  

In the example I posted, which uses Boost.Beast, you do need the strand for each connection because of the `beast::tcp_stream` timeout functionality it's using. You need to ask yourself "am I running two parallel operations sharing data?", and if the answer is yes, you need the strand. It's not obvious from the code, but calling `expires_after` ends up running a timer operation concurrently with whatever read or write the stream is doing, so you do need the strand. If you use `asio::cancel_after`, the same considerations apply. If you weren't using timeouts, you wouldn't need the strand.

---

## Comment 7

> Username: nestal  
> Created at: 2024-12-20 12:21:24 UTC  
> Url: https://github.com/boostorg/redis/issues/224#issuecomment-2556902276  

This is all I need. Thanks @anarthal . Closing
