# #271 - always collapsing, Can you tell me the reason？ [Closed]

> Username: jphz  
> Created at: 2024-05-22 14:34:22 UTC  
> Updated at: 2024-05-23 11:46:56 UTC  
> Closed at: 2024-05-23 11:46:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/271  

```cpp  
#include <iostream>  
  
#include "boost/asio.hpp"  
#include "boost/asio/spawn.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
int main() {  
    boost::asio::thread_pool th_pool(10);  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "123456";  
    params.database = "boat";  
    params.initial_size = 1;  
    params.max_size = 100;  
    static auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(th_pool.get_executor()), std::move(params));  
  
    conn_pool->async_run(boost::asio::detached);  
  
    while (true) {  
        for (int i = 0; i < 100; ++i) {  
            boost::asio::spawn(boost::asio::make_strand(th_pool.get_executor()), [&](boost::asio::yield_context yield) {  
                boost::system::error_code ec;  
                boost::mysql::pooled_connection conn = conn_pool->async_get_connection(yield[ec]);  
                if (ec) {  
                    return;  
                }  
  
                static boost::mysql::diagnostics _errorInfo{};  
                static boost::mysql::results _results{};  
                conn->async_execute("SELECT COUNT(*) FROM `character`;", _results, _errorInfo, yield[ec]);  
                if (ec) {  
                    return;  
                }  
  
                for (auto rows : _results.rows()) {  
                    for (std::size_t i = 0; i < rows.size(); ++i) {  
                        auto field = rows[i];  
                        std::cout << "async_execute:" << std::this_thread::get_id() << " : " << field.as_int64()  
                                  << std::endl;  
                    }  
                }  
            });  
        }  
        std::this_thread::sleep_for(std::chrono::seconds(1));  
    }  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    th_pool.stop();  
    conn_pool->cancel();  
    return 0;  
}  
```

---

## Comment 1

> Username: jphz  
> Created at: 2024-05-22 14:36:15 UTC  
> Updated at: 2024-05-22 14:36:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/271#issuecomment-2124967393  

Access permission conflict  
![image](https://github.com/boostorg/mysql/assets/30751794/457b7828-5a87-4226-8f3e-ebe37e9c06bd)

---

## Comment 2

> Username: jphz  
> Created at: 2024-05-22 14:44:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/271#issuecomment-2124985592  

Do you have multiple threads open internally?

---

## Comment 3

> Username: jphz  
> Created at: 2024-05-22 14:46:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/271#issuecomment-2124990395  

I want to use multithreading, I have learned your code case, but it keeps crashing at the moment

---

## Comment 4

> Username: anarthal  
> Created at: 2024-05-22 14:48:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/271#issuecomment-2124995237  

From your example, you have a single `static` variable shared between 100 coroutines, which is definitely causing trouble. I'll rewrite your example in some minutes.

---

## Comment 5

> Username: anarthal  
> Created at: 2024-05-22 14:55:22 UTC  
> Updated at: 2024-05-22 14:56:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/271#issuecomment-2125011176  

Making your variables non-static works as expected:  
  
```cpp  
#include <iostream>  
  
#include "boost/asio.hpp"  
#include "boost/asio/spawn.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
int main()  
{  
    boost::asio::thread_pool th_pool(10);  
    boost::mysql::pool_params params{};  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "123456";  
    params.database = "boat";  
    params.initial_size = 1;  
    params.max_size = 100;  
  
    // No need to use static here  
    auto conn_pool = std::make_shared<boost::mysql::connection_pool>(  
        boost::mysql::pool_executor_params::thread_safe(th_pool.get_executor()),  
        std::move(params)  
    );  
  
    conn_pool->async_run(boost::asio::detached);  
  
    while (true)  
    {  
        for (int i = 0; i < 100; ++i)  
        {  
            boost::asio::spawn(  
                // No need for a strand here, as you're no longer using static variables  
                th_pool,  
                [&](boost::asio::yield_context yield) {  
                    boost::system::error_code ec;  
                    boost::mysql::pooled_connection conn = conn_pool->async_get_connection(yield[ec]);  
                    if (ec)  
                    {  
                        return;  
                    }  
  
                    // Don't use static variables here, as this causes a single variable  
                    // to be used across all your coroutines, causing race conditions  
                    boost::mysql::diagnostics _errorInfo{};  
                    boost::mysql::results _results{};  
                    conn->async_execute("SELECT COUNT(*) FROM `character`;", _results, _errorInfo, yield[ec]);  
                    if (ec)  
                    {  
                        return;  
                    }  
  
                    for (auto rows : _results.rows())  
                    {  
                        for (std::size_t i = 0; i < rows.size(); ++i)  
                        {  
                            auto field = rows[i];  
                            std::cout << "async_execute:" << std::this_thread::get_id() << " : "  
                                      << field.as_int64() << std::endl;  
                        }  
                    }  
                }  
            );  
        }  
        std::this_thread::sleep_for(std::chrono::seconds(1));  
    }  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    th_pool.stop();  
    conn_pool->cancel();  
    return 0;  
}  
```

---

## Comment 6

> Username: jphz  
> Created at: 2024-05-22 15:48:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/271#issuecomment-2125130401  

Thank you.
