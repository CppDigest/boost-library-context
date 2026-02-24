# #260 - conn_pool async_execute not found result [Closed]

> Username: jphz  
> Created at: 2024-05-11 05:42:33 UTC  
> Updated at: 2024-06-05 17:01:03 UTC  
> Closed at: 2024-05-18 15:31:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/260  

```cpp  
#include <iostream>  
  
#include "boost/asio.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
int main() {  
    boost::asio::io_context ctx{};  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "123456";  
    params.database = "anubis";  
    static auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(ctx.get_executor()), std::move(params));  
    if (!conn_pool->valid()) {  
        return 1;  
    }  
  
    conn_pool->async_run(boost::asio::detached);  
  
    conn_pool->async_get_connection([&](boost::mysql::error_code ec, boost::mysql::pooled_connection conn) {  
        if (ec) {  
            return;  
        }  
  
        // query result ok  
        boost::mysql::diagnostics _errorInfo{};  
        boost::mysql::results _results{};  
        conn->execute("SELECT COUNT(*) FROM `character`;", _results, ec, _errorInfo);  
        if (ec) {  
            return;  
        }  
  
        for (auto rows : _results.rows()) {  
            for (std::size_t i = 0; i < rows.size(); ++i) {  
                auto field = rows[i];  
                std::cout << "execute:" << field.as_int64() << std::endl;  
            }  
        }  
  
        // query not result  
        static boost::mysql::diagnostics _errorInfo2{};  
        static boost::mysql::results _results2{};  
        conn->async_execute("SELECT COUNT(*) FROM `character`;", _results2, _errorInfo2,  
                            [&](boost::mysql::error_code ec) {  
                                if (ec) {  
                                    return;  
                                }  
  
                                // this rows is 0  
                                for (auto rows : _results2.rows()) {  
                                    for (std::size_t i = 0; i < rows.size(); ++i) {  
                                        auto field = rows[i];  
                                        std::cout << "async_execute:" << field.as_int64() << std::endl;  
                                    }  
                                }  
                            });  
    });  
  
    ctx.run();  
    return 0;  
}  
```  
![image](https://github.com/boostorg/mysql/assets/30751794/6eb33225-910e-443a-a366-df21e3d24eda)

---

## Comment 1

> Username: jphz  
> Created at: 2024-05-11 05:44:23 UTC  
> Updated at: 2024-05-11 05:45:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2105575654  

os: windows 10  
mysq: 8.0.33.0  
ide: vs2022

---

## Comment 2

> Username: jphz  
> Created at: 2024-05-11 08:02:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2105625142  

async_execute     Why did the interface not complete and return early？

---

## Comment 3

> Username: jphz  
> Created at: 2024-05-11 08:06:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2105626719  

Is there a complete example that I can learn to use connection_pool。

---

## Comment 4

> Username: anarthal  
> Created at: 2024-05-11 11:34:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2105687525  

Hi! As a general rule, calling sync functions within callbacks may produce unpredictable results. You better go either fully sync or fully async. When working with `connection_pool`, better to go fully async. For instance:  
  
```  
#include <iostream>  
  
#include "boost/asio.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
int main() {  
    boost::asio::io_context ctx{};  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "123456";  
    params.database = "anubis";  
    auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(ctx.get_executor()), std::move(params));  
  
    conn_pool->async_run(boost::asio::detached);  
  
    // You need to define these here so they are kept alive until the operations complete  
    boost::mysql::diagnostics _errorInfo2{};  
    boost::mysql::results _results2{};  
  
    conn_pool->async_get_connection([&](boost::mysql::error_code ec, boost::mysql::pooled_connection conn) {  
        if (ec) {  
            std::cout << "Error getting connection: " << ec << std::endl;  
            return;  
        }  
  
  
        conn->async_execute("SELECT COUNT(*) FROM `character`;", _results2, _errorInfo2,  
                            [&](boost::mysql::error_code ec) {  
                                if (ec) {  
                                    std::cout << "Error executing query: " << ec << std::endl;  
                                    return;  
                                }  
  
                                // this rows is 0  
                                for (auto rows : _results2.rows()) {  
                                    for (std::size_t i = 0; i < rows.size(); ++i) {  
                                        auto field = rows[i];  
                                        std::cout << "async_execute:" << field.as_int64() << std::endl;  
                                    }  
                                }  
                            });  
    });  
  
    ctx.run();  
    return 0;  
}  
```  
  
Notice a couple of things:  
* You don't need the `is_valid()` check when constructing the pool. `is_valid()` is used to detect moved-from pools. You're not moving the pool, so you don't need it.  
* I've moved the variables out of the inner lambdas into main.  
  
Now, this can get overwhelming pretty fast. Since you're in a modern programming environment, you can use C++20 coroutines instead of callbacks. Your code will look sync, but will behave asynchronously. For instance:  
  
```  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/deferred.hpp>  
  
#include <exception>  
#include <iostream>  
  
#include "boost/asio/detached.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
// This is your coroutine. Place your application code here  
boost::asio::awaitable<void> run_query(boost::mysql::connection_pool& pool)  
{  
    // Get a connection from the pool. deferred is used to transform an async function  
    // into a coroutine, which you can co_await. If the function fails, an exception is thrown.  
    // The coroutine gets suspended until the operation finishes. When the operation completes,  
    // the function resumes just after the co_await. Local variables are kept alive,  
    // so you don't need to think about lifetimes  
    boost::mysql::pooled_connection conn = co_await pool.async_get_connection(boost::asio::deferred);  
  
    // No need to declare this static  
    boost::mysql::results result{};  
    co_await conn->async_execute("SELECT 1;", result, boost::asio::deferred);  
  
    // this rows is 0  
    for (auto rows : result.rows())  
    {  
        for (std::size_t i = 0; i < rows.size(); ++i)  
        {  
            auto field = rows[i];  
            std::cout << "async_execute:" << field.as_int64() << std::endl;  
        }  
    }  
  
    // If you want your application to exit once your query finishes, you need to  
    // call pool.cancel(). You would usually do this when you want your server to  
    // exit - e.g. when you receive a SIGINT signal  
    pool.cancel();  
}  
  
int main()  
{  
    boost::asio::io_context ctx{};  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "";  
    params.database = "boost_mysql_examples";  
    auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(ctx.get_executor()),  
        std::move(params)  
    );  
  
    conn_pool->async_run(boost::asio::detached);  
  
    // Spawn a new coroutine  
    boost::asio::co_spawn(  
        // Pass in the io_context where you're running the coroutine...  
        ctx,  
  
        // A function with signature awaitable<void>() indicating what to run...  
        [conn_pool] { return run_query(*conn_pool); },  
  
        // If the coroutine throws an exception, propagate it to main  
        [](std::exception_ptr e) {  
            if (e)  
                std::rethrow_exception(e);  
        }  
    );  
  
    ctx.run();  
}  
```  
  
There is a full connection pool example here: https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/examples/connection_pool.html. This one uses stackful coroutines, which are similar to what I posted, only require C++11, but require linking your executable to Boost.Context. Working with these coroutines is almost the same as with C++20 ones. For instance, this is what your example would look like with stackful coroutines:  
  
```  
#include <boost/asio/deferred.hpp>  
#include <boost/asio/spawn.hpp>  
  
#include <exception>  
#include <iostream>  
  
#include "boost/asio/detached.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
// This is your coroutine. Place your application code here.  
// You need an additional parameter here: the yield_context  
void run_query(boost::mysql::connection_pool& pool, boost::asio::yield_context yield)  
{  
    // When passing the special value yield to the async function,  
    // you make the function a coroutine. This works like the previous example.  
    boost::mysql::pooled_connection conn = pool.async_get_connection(yield);  
  
    boost::mysql::results result{};  
    conn->async_execute("SELECT 1;", result, yield);  
  
    // this rows is 0  
    for (auto rows : result.rows())  
    {  
        for (std::size_t i = 0; i < rows.size(); ++i)  
        {  
            auto field = rows[i];  
            std::cout << "async_execute:" << field.as_int64() << std::endl;  
        }  
    }  
  
    // If you want your application to exit once your query finishes, you need to  
    // call pool.cancel(). You would usually do this when you want your server to  
    // exit - e.g. when you receive a SIGINT signal  
    pool.cancel();  
}  
  
int main()  
{  
    boost::asio::io_context ctx{};  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "";  
    params.database = "boost_mysql_examples";  
    auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(ctx.get_executor()),  
        std::move(params)  
    );  
  
    conn_pool->async_run(boost::asio::detached);  
  
    // Spawn a new coroutine. This time we're using spawn, instead of co_spawn  
    boost::asio::spawn(  
        // Pass in the io_context where you're running the coroutine...  
        ctx,  
  
        // A function with signature void(yield_context) indicating what to run...  
        [conn_pool] (boost::asio::yield_context yield) { return run_query(*conn_pool, yield); },  
  
        // If the coroutine throws an exception, propagate it to main  
        [](std::exception_ptr e) {  
            if (e)  
                std::rethrow_exception(e);  
        }  
    );  
  
    ctx.run();  
}  
```  
  
You can also take a look at [this project](https://github.com/anarthal/servertech-chat), which is a fully-blown chat server using Asio, Beast, MySQL, Redis and stackful coroutines.  
  
Please let me know if that worked for you or if you have any more questions.  
  
Regards,  
Ruben.

---

## Comment 5

> Username: jphz  
> Created at: 2024-06-05 12:35:10 UTC  
> Updated at: 2024-06-05 12:37:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149731011  

```cpp  
#include <iostream>  
  
#include "boost/asio.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
int main() {  
    boost::asio::io_context ctx{};  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
    params.username = "root";  
    params.password = "123456";  
    params.database = "boat";  
    auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(ctx.get_executor()), std::move(params));  
  
    conn_pool->async_run(boost::asio::detached);  
  
    std::thread th([&]() {  
        ctx.run();  
    });  
  
    boost::mysql::diagnostics error_info{};  
    boost::mysql::results results{};  
    conn_pool->async_get_connection(  
        std::chrono::seconds(0), [&](boost::mysql::error_code ec, boost::mysql::pooled_connection conn) {  
            std::cout << "step 1" << std::endl;  
            if (ec) {  
                std::cout << "Error getting connection: " << ec << std::endl;  
                return;  
            }  
  
            conn->async_execute(  
                "SELECT COUNT(*) FROM `character`;", results, error_info, [&](boost::mysql::error_code ec) {  
                    std::cout << "step 2" << std::endl;  
  
                    if (ec) {  
                        std::cout << "Error executing query: " << ec.value() << ":" << ec.message() << std::endl;  
                        return;  
                    }  
  
                    std::cout << "step 3 row_size:" << results.rows().size() << std::endl;  
                    for (auto rows : results.rows()) {  
                        for (std::size_t i = 0; i < rows.size(); ++i) {  
                            auto field = rows[i];  
                            std::cout << "async_execute:" << field.as_int64() << std::endl;  
                        }  
                    }  
                });  
        });  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    ctx.stop();  
    th.join();  
    return 0;  
}  
```

---

## Comment 6

> Username: jphz  
> Created at: 2024-06-05 12:35:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149731778  

Unable to query results

---

## Comment 7

> Username: jphz  
> Created at: 2024-06-05 12:36:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149733639  

![image](https://github.com/boostorg/mysql/assets/30751794/5aa56fa8-5674-4b6c-bc87-bb2db8d23066)

---

## Comment 8

> Username: jphz  
> Created at: 2024-06-05 12:36:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149736287  

![image](https://github.com/boostorg/mysql/assets/30751794/a66514c5-324b-4061-86a2-8294a30e0965)

---

## Comment 9

> Username: jphz  
> Created at: 2024-06-05 12:46:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149767195  

There's no problem using coroutine, but I don't want to use coroutine, I just want to use asynchronous methods.

---

## Comment 10

> Username: anarthal  
> Created at: 2024-06-05 12:49:09 UTC  
> Updated at: 2024-06-05 12:49:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149775249  

Your `conn` variable is getting destroyed right after calling `conn->execute`, which makes the connection to be returned to the pool while you're still using it, causing undefined behavior. This should work:  
  
```cpp  
#include <iostream>  
  
#include "boost/asio.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
int main() {  
    boost::asio::io_context ctx{};  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
    params.username = "root";  
    params.password = "123456";  
    params.database = "boat";  
    auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(ctx.get_executor()), std::move(params));  
  
    conn_pool->async_run(boost::asio::detached);  
  
    std::thread th([&]() {  
        ctx.run();  
    });  
  
    boost::mysql::diagnostics error_info{};  
    boost::mysql::results results{};  
    boost::mysql::pooled_connection conn;  
    conn_pool->async_get_connection(  
        std::chrono::seconds(0), [&](boost::mysql::error_code ec, boost::mysql::pooled_connection c) {  
            conn = std::move(c);  
            std::cout << "step 1" << std::endl;  
            if (ec) {  
                std::cout << "Error getting connection: " << ec << std::endl;  
                return;  
            }  
  
            conn->async_execute(  
                "SELECT COUNT(*) FROM `character`;", results, error_info, [&](boost::mysql::error_code ec) {  
                    std::cout << "step 2" << std::endl;  
  
                    if (ec) {  
                        std::cout << "Error executing query: " << ec.value() << ":" << ec.message() << std::endl;  
                        return;  
                    }  
  
                    std::cout << "step 3 row_size:" << results.rows().size() << std::endl;  
                    for (auto rows : results.rows()) {  
                        for (std::size_t i = 0; i < rows.size(); ++i) {  
                            auto field = rows[i];  
                            std::cout << "async_execute:" << field.as_int64() << std::endl;  
                        }  
                    }  
                });  
        });  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    ctx.stop();  
    th.join();  
    return 0;  
}  
  
```

---

## Comment 11

> Username: jphz  
> Created at: 2024-06-05 12:51:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149783441  

I need an asynchronous code that can execute queries in a multi-threaded environment.

---

## Comment 12

> Username: jphz  
> Created at: 2024-06-05 12:57:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149801812  

I should know what to do now, thank you.

---

## Comment 13

> Username: anarthal  
> Created at: 2024-06-05 13:00:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149814700  

If you're going to use callbacks, I'd advise to create a session object on the heap, storing all intermediate state that must be kept alive until the operation completes. For instance:  
  
```cpp  
  
class session : public std::enable_shared_from_this<session> {  
    boost::mysql::connection_pool& pool_;  
    boost::mysql::pooled_connection conn_;  
    boost::mysql::diagnostics error_info_;  
    boost::mysql::results results_;  
  
    void print_results()  
    {  
        std::cout << "step 3 row_size:" << results.rows().size() << std::endl;  
        for (auto rows : results.rows()) {  
            for (std::size_t i = 0; i < rows.size(); ++i) {  
                auto field = rows[i];  
                std::cout << "async_execute:" << field.as_int64() << std::endl;  
            }  
        }  
    }  
public:  
    session() = default;  
  
    void start(boost::mysql::connection_pool& pool)  
    {  
        pool.async_get_connection(  
            std::chrono::seconds(0), [self = this->shared_from_this()](boost::mysql::error_code ec, boost::mysql::pooled_connection c) {  
                self->conn = std::move(c);  
                std::cout << "step 1" << std::endl;  
                if (ec) {  
                    std::cout << "Error getting connection: " << ec << std::endl;  
                    return;  
                }  
                self->execute();  
            }  
        );  
    }  
  
    void execute()  
    {  
        conn->async_execute(  
            "SELECT COUNT(*) FROM `character`;",  
            results_,  
            error_info_,  
            [self = this->shared_from_this()](boost::mysql::error_code ec) {  
                std::cout << "step 2" << std::endl;  
                if (ec) {  
                    std::cout << "Error executing query: " << ec.value() << ":" << ec.message() << std::endl;  
                    return;  
                }  
                self->print_results();  
            }  
        );  
    }  
};  
  
int main() {  
    boost::asio::io_context ctx{};  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
    params.username = "root";  
    params.password = "123456";  
    params.database = "boat";  
    auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(ctx.get_executor()), std::move(params));  
  
    conn_pool->async_run(boost::asio::detached);  
  
    auto session = std::make_shared<session>();  
    session->start(*conn_pool);  
    ctx.run();  
    return 0;  
}  
```  
  
I haven't built the code, it may contain typos.

---

## Comment 14

> Username: jphz  
> Created at: 2024-06-05 13:04:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2149828943  

Yes, I understand. I used to think that boost:: mysql:: pooled_connection was managed internally.

---

## Comment 15

> Username: anarthal  
> Created at: 2024-06-05 16:09:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2150446521  

Destroying a `pooled_connection` is how you tell the pool that you're done using the connection and it can be recycled.

---

## Comment 16

> Username: jphz  
> Created at: 2024-06-05 17:01:01 UTC  
> Url: https://github.com/boostorg/mysql/issues/260#issuecomment-2150541122  

Yeah, I think I know how to handle this, thank you.
