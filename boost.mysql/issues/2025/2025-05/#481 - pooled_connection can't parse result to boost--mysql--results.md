# #481 - pooled_connection can't parse result to boost::mysql::results [Closed]

> Username: Nu11able  
> Created at: 2025-05-14 03:35:33 UTC  
> Updated at: 2025-05-15 16:01:55 UTC  
> Closed at: 2025-05-15 15:41:18 UTC  
> Url: https://github.com/boostorg/mysql/issues/481  

This is my code, a simple query from mysql table `world`, when I try to use boost::mysql::results`result3` to receive result, the code will stuck at line `conn->async_execute`, However, if I use `boost::mysql::static_results<boost::mysql::pfr_by_name<City>> result2` to receive result, everything works fine. I read the doc but can't find anything mentioned that `boost::mysql::results` can't be used with `connection_pool`. I don't know if this is bug, any reply would be appreciated  
  
- compiler： gcc14.2  
- boost version: 1.87  
- OS: centos7 3.10.0-1127.el7.x86_64  
- mysql version: 8.4.5  
  
```cpp  
struct City {  
    int ID;  
    std::string Name;  
    std::string CountryCode;  
    std::string District;  
    int Population;  
};  
  
boost::mysql::diagnostics diag;  
boost::mysql::static_results<boost::mysql::pfr_by_name<City>> result2;  
boost::mysql::results result3;  
// auto [ec2] = co_await conn->async_execute(boost::mysql::with_params("select * from city where `Name`='Kabul';"), result2, diag, boost::asio::as_tuple);  
auto [ec2] = co_await conn->async_execute(boost::mysql::with_params("select * from city where `Name`='Kabul';"), result3, diag, boost::asio::as_tuple);  
if(ec2) {  
	SPDLOG_ERROR("{} get conn failed: code:{}, {} diag:{} | {}", __FUNCTION__, ec2.value(), ec2.message(), diag.client_message(), diag.server_message());  
}  
auto time_end = std::chrono::system_clock::now().time_since_epoch().count();  
// SPDLOG_INFO("{} time:{}, result:{}", __FUNCTION__, time_end - time_begin, result2.rows()[0].Population);  
SPDLOG_INFO("{} time:{}, result:{}", __FUNCTION__, time_end - time_begin, result3.rows().at(0).at(0).as_string());  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2025-05-14 08:49:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/481#issuecomment-2879358047  

This should definitely not be the case. `results` is supported with `pooled_connection`. Can you please share your code regarding how you obtain and store `conn`? The destructor for `pooled_connection` returns the obtained connection to the pool. If you're somehow letting the `pooled_connection` object die (e.g. by storing only a reference), you might get an error like the one you describe.  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: Nu11able  
> Created at: 2025-05-14 16:34:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/481#issuecomment-2880868944  

> This should definitely not be the case. `results` is supported with `pooled_connection`. Can you please share your code regarding how you obtain and store `conn`? The destructor for `pooled_connection` returns the obtained connection to the pool. If you're somehow letting the `pooled_connection` object die (e.g. by storing only a reference), you might get an error like the one you describe.  
>   
> Thanks, Ruben.  
  
Thanks for your reply. I found that the `as_xxx()` member function of `results` needs to match the column type in MySQL; otherwise, it gets stuck at `result3.rows().at(0).at(0).as_string()`. I changed `as_string()` to `as_int64()`, and now it works. That was my mistake — thanks a lot!

---

## Comment 3

> Username: anarthal  
> Created at: 2025-05-14 16:42:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/481#issuecomment-2880887624  

`as_xxx` throws an exception when a type mismatch is detected. What you're observing probably means that you're swallowing exceptions somewhere. For example, if you have:  
  
```cpp  
asio::io_context ctx;  
asio::co_spawn(ctx, my_coro(), asio::detached);  
```  
  
This will swallow exceptions. You might want to change it to:  
  
```cpp  
asio::io_context ctx;  
asio::co_spawn(ctx, my_coro(), [](std::exception_ptr exc) { if (exc) std::rethrow_exception(exc); });  
```  
  
To allow for better diagnostics.

---

## Comment 4

> Username: Nu11able  
> Created at: 2025-05-15 14:06:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/481#issuecomment-2883946177  

> `as_xxx` throws an exception when a type mismatch is detected. What you're observing probably means that you're swallowing exceptions somewhere. For example, if you have:  
>   
> asio::io_context ctx;  
> asio::co_spawn(ctx, my_coro(), asio::detached);  
> This will swallow exceptions. You might want to change it to:  
>   
> asio::io_context ctx;  
> asio::co_spawn(ctx, my_coro(), [](std::exception_ptr exc) { if (exc) std::rethrow_exception(exc); });  
> To allow for better diagnostics.  
  
Thank you so much for your help! By the way, I have another question. Since `connection_pool::async_run` always returns success, what happens if I provide a wrong password or an incorrect MySQL hostname, making it unable to connect to the MySQL server? Later, when I try to run `async_get_connection`, it gets stuck because no connection is available.  
  
How can I detect such an error—i.e., that the `connection_pool` was not initialized successfully?  
This is my example code.  
```cpp  
#include <boost/mysql.hpp>  
#include <boost/mysql/pfr.hpp>  
#include <boost/asio.hpp>  
  
#include <spdlog/spdlog.h>  
  
using namespace boost;  
  
struct City {  
    int ID;  
    std::string Name;  
    std::string CountryCode;  
    std::string District;  
    int Population;  
};  
  
asio::awaitable<void> QueryTest(mysql::connection_pool& pool) {  
    mysql::diagnostics diag;  
    auto [ec, conn] = co_await pool.async_get_connection(diag, asio::as_tuple);  
    if (ec) {  
        SPDLOG_INFO("get conn failed, code:{}, msg:{}", ec.value(), ec.message());  
        co_return;  
    }  
    mysql::static_results<boost::mysql::pfr_by_name<City>> static_result;  
    boost::mysql::results result;  
    SPDLOG_INFO("begin query");  
    auto [ec2] = co_await conn->async_execute(  
        "select * from city where `Name`='Kabul'",   
        result, diag, boost::asio::as_tuple);  
    SPDLOG_INFO("end query");  
      
    if(ec2) {  
        SPDLOG_ERROR("query failed: code:{}, {} diag:{} | {}", ec2.value(), ec2.message(), diag.client_message(), diag.server_message());  
        co_return;  
    }  
    SPDLOG_INFO("result:{}", result.rows().at(0).at(0).as_int64());  
    // SPDLOG_INFO("result:{}", static_result.rows()[0].ID);  
    SPDLOG_INFO("query finish");  
}  
  
  
int main() {  
    asio::io_context context;  
    mysql::pool_params params;  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "mysqlpassword";  
    params.database = "world";  
    mysql::connection_pool pool(context, std::move(params));  
    pool.async_run(asio::detached); // always return success  
    asio::co_spawn(context, QueryTest(pool), [](std::exception_ptr exc) { if (exc) std::rethrow_exception(exc); });  
  
    context.run();  
  
    return 0;  
}  
```

---

## Comment 5

> Username: anarthal  
> Created at: 2025-05-15 14:15:53 UTC  
> Updated at: 2025-05-15 14:18:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/481#issuecomment-2883977014  

Yep! The recommended approach is using `asio::cancel_after`. `async_get_connection` has special logic to handle cancellations, and will output you (in the `diagnostics&` output parameter or in the thrown exception) information about what happened exactly. To be precise, if one of the connections encountered an error while connecting (like an authentication failure due to an invalid password), that error will be included in the diagnostics object.  
  
You can get some inspiration from the tutorial about this: https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/tutorial_connection_pool.html  
  
You might also find interesting the error handling tutorial: https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/tutorial_error_handling.html

---

## Comment 6

> Username: Nu11able  
> Created at: 2025-05-15 15:32:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/481#issuecomment-2884258138  

I truly appreciate your patient and detailed response. I've now successfully implemented logic to ensure the server doesn't start if the connection pool initialization fails, and to log the reason for the failure in `InitConnPool`. Thanks again for your kind support.   
  
  
```cpp  
#include <thread>  
#include <chrono>  
#include <boost/mysql.hpp>  
#include <boost/mysql/pfr.hpp>  
#include <boost/asio.hpp>  
#include <spdlog/spdlog.h>  
  
using namespace boost;  
using namespace std::chrono_literals;  
  
struct City {  
    int ID;  
    std::string Name;  
    std::string CountryCode;  
    std::string District;  
    int Population;  
};  
  
asio::awaitable<void> QueryTest(mysql::connection_pool& pool) {  
    mysql::diagnostics diag;  
    auto [ec, conn] = co_await pool.async_get_connection(diag, asio::as_tuple);  
    if (ec) {  
        SPDLOG_INFO("get conn failed, code:{}, msg:{}", ec.value(), ec.message());  
        co_return;  
    }  
    mysql::static_results<boost::mysql::pfr_by_name<City>> static_result;  
    boost::mysql::results result;  
    auto [ec2] = co_await conn->async_execute(  
        "select * from city where `Name`='Kabul'",   
        result, diag, boost::asio::as_tuple);  
      
    if(ec2) {  
        SPDLOG_ERROR("query failed: code:{}, {} diag:{} | {}", ec2.value(), ec2.message(), diag.client_message(), diag.server_message());  
        co_return;  
    }  
    SPDLOG_INFO("result:{}", result.rows().at(0).at(0).as_int64());  
    // SPDLOG_INFO("result:{}", static_result.rows()[0].ID);  
}  
  
asio::awaitable<std::shared_ptr<mysql::connection_pool>> InitConnPool() {  
    mysql::pool_params params;  
    params.server_address.emplace_host_and_port("localhost");  
    params.username = "root";  
    params.password = "wrongpassword";  
    params.database = "world";  
    auto pool_ptr = std::make_shared<mysql::connection_pool>(co_await asio::this_coro::executor, std::move(params));  
    pool_ptr->async_run(asio::detached);  
    mysql::diagnostics diag;  
    auto [ec, conn] = co_await pool_ptr->async_get_connection(diag, asio::cancel_after(2s, asio::as_tuple));  
    if (ec) {  
        SPDLOG_ERROR("diag:{}, {}",  diag.client_message(), diag.server_message());  
        co_return nullptr;  
    }  
    co_return pool_ptr;  
}  
  
int main() {  
    asio::io_context context;  
    auto pool_future = asio::co_spawn(context, InitConnPool(), asio::use_future);  
    std::thread ctx_thread([&context](){ context.run(); });  
  
    auto pool_ptr = pool_future.get();  
    if (pool_ptr) {  
        // if pool initialized successfully  
        asio::co_spawn(context, QueryTest(*pool_ptr), [](std::exception_ptr exc) { if (exc) std::rethrow_exception(exc); });  
    }  
    else {  
        // otherwise, don't start the server  
        SPDLOG_ERROR("Pool initialize failed");  
    }  
  
    ctx_thread.join();  
    return 0;  
}  
```  
  
```  
[2025-05-15 23:21:07.081] [error] [main.cpp:51] diag:, Last connection attempt failed with: er_access_denied_error [mysql.common-server:1045]: Access denied for user 'root'@'hello' (using password: YES)  
[2025-05-15 23:21:07.081] [error] [main.cpp:69] Pool initialize failed  
```

---

## Comment 7

> Username: anarthal  
> Created at: 2025-05-15 16:01:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/481#issuecomment-2884347703  

Welcome. You might also consider calling run() in main to save a thread, too.  
  
Should you need any more help, feel free to open another issue.
