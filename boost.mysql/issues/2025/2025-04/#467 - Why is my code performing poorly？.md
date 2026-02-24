# #467 - Why is my code performing poorly？ [Closed]

> Username: jphz  
> Created at: 2025-04-12 06:27:07 UTC  
> Updated at: 2025-04-15 16:57:45 UTC  
> Closed at: 2025-04-15 16:57:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/467  

boost_version: 1.88.0  
os_system: ubuntu22.04  
  
I use the original MYSQL C API with the same number of threads instead of Boost MYSQL performs much better.  
  
MYSQL C API: 258ms  
  
BOOST.MYSQL: 3565ms  
  
```cpp  
#include <cstdint>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
  
#include "boost/asio/awaitable.hpp"  
#include "boost/asio/co_spawn.hpp"  
#include "boost/asio/detached.hpp"  
#include "boost/asio/redirect_error.hpp"  
#include "boost/asio/thread_pool.hpp"  
#include "boost/mysql/connection_pool.hpp"  
  
class DBPool final : public std::enable_shared_from_this<DBPool> {  
public:  
    using Callback = std::function<void(boost::system::error_code, boost::mysql::results& results)>;  
  
    DBPool()  
        : m_threadPool(8) {  
    }  
  
    ~DBPool() {  
        stop();  
    }  
  
    bool start() {  
        if (m_running) {  
            return false;  
        }  
  
        m_running = true;  
  
        boost::mysql::pool_params params{};  
        params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
        params.username = "root";  
        params.password = "123456";  
        params.database = "boat";  
        params.ssl = static_cast<boost::mysql::ssl_mode>(0);  
        params.connect_timeout = std::chrono::seconds(0);  
        params.thread_safe = true;  
  
        m_dbPool = std::make_shared<boost::mysql::connection_pool>(m_threadPool, std::move(params));  
        m_dbPool->async_run(boost::asio::detached);  
  
        std::cout << "DBPool::start success" << std::endl;  
        return true;  
  
        return false;  
    }  
  
    void stop() {  
        if (!m_running) {  
            return;  
        }  
  
        m_running = false;  
  
        if (m_dbPool) {  
            m_dbPool->cancel();  
            m_dbPool = nullptr;  
        }  
  
        m_threadPool.stop();  
        std::cout << "DBPool::stop success" << std::endl;  
    }  
  
    void query(std::string_view sql, Callback&& callback) {  
        if (!m_dbPool) {  
            return;  
        }  
  
        boost::asio::co_spawn(  
            m_dbPool->get_executor(),  
            [this, self = shared_from_this(), sql, callback = std::move(callback)]() -> boost::asio::awaitable<void> {  
                boost::system::error_code ec{};  
                boost::mysql::diagnostics errorInfo{};  
                boost::mysql::results results{};  
                auto connection = co_await m_dbPool->async_get_connection(  
                    boost::asio::redirect_error(boost::asio::use_awaitable, ec));  
                if (ec) {  
                    callback(ec, results);  
                    co_return;  
                }  
  
                co_await connection->async_execute(sql, results, errorInfo,  
                                                   boost::asio::redirect_error(boost::asio::use_awaitable, ec));  
                callback(ec, results);  
            },  
            boost::asio::detached);  
    }  
  
private:  
    bool m_running{};  
    std::shared_ptr<boost::mysql::connection_pool> m_dbPool{};  
    boost::asio::thread_pool m_threadPool{};  
};  
  
static int64_t getMillisecond() {  
    auto now = std::chrono::system_clock::now();  
    auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(now.time_since_epoch());  
    return duration.count();  
}  
  
int main() {  
    static auto gameDB = std::make_shared<DBPool>();  
    gameDB->start();  
    static std::size_t startTime = getMillisecond();  
    static std::string sql = "SELECT VERSION()";  
    for (int i = 0; i < 10000; ++i) {  
        gameDB->query(sql, [](boost::system::error_code ec, boost::mysql::results&) {  
            static std::atomic_int count = 0;  
            if (++count >= 10000) {  
                std::cout << "use_time:" << getMillisecond() - startTime << std::endl;  
            }  
        });  
    }  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    gameDB->stop();  
    return 0;  
}  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2025-04-12 17:11:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2798911231  

Can you please share the code using `libmysqlclient` so we can compare? They must be functionally distinct for such a difference to be observed (AFAIK `libmysqlclient` doesn't expose connection pooling).  
  
From the top of my head:  
* Since `connection_pool` is async, increasing the number of threads doesn't necessarily make things faster. I'd start with `thread_safe=false` and a single thread.  
* `connection_pool` resets connections by default when they're returned to the pool, to clean up any leftover state. If you didn't alter session state (as is your case), on success, you can use `pooled_connection::return_without_reset` when you're done with connections to skip the reset operation.  
* Prefer `asio::deferred` to `asio::use_awaitable` in coroutines, as it implies less memory allocations.

---

## Comment 2

> Username: jphz  
> Created at: 2025-04-12 17:46:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2798923814  

> Can you please share the code using `libmysqlclient` so we can compare? They must be functionally distinct for such a difference to be observed (AFAIK `libmysqlclient` doesn't expose connection pooling).  
>   
> From the top of my head:  
>   
> * Since `connection_pool` is async, increasing the number of threads doesn't necessarily make things faster. I'd start with `thread_safe=false` and a single thread.  
> * `connection_pool` resets connections by default when they're returned to the pool, to clean up any leftover state. If you didn't alter session state (as is your case), on success, you can use `pooled_connection::return_without_reset` when you're done with connections to skip the reset operation.  
> * Prefer `asio::deferred` to `asio::use_awaitable` in coroutines, as it implies less memory allocations.  
  
  
This is the code I wrote using libmysqlclient。  
#include "concurrentqueue/blockingconcurrentqueue.h"  
use third header  
https://github.com/cameron314/concurrentqueue  
  
```cpp  
  
#include <cstdint>  
#include <memory>  
#include <string>  
#include <string_view>  
#include <thread>  
  
#include "boost/asio/io_context.hpp"  
#include "boost/asio/post.hpp"  
#include "boost/asio/thread_pool.hpp"  
#include "mysql/errmsg.h"  
#include "mysql/mysql.h"  
  
constexpr uint16_t MYSQL_RECONNECT_TIMES = 100;  
constexpr uint16_t MYSQL_RECONNECT_COUNT = 10;  
  
class DBConnect final {  
public:  
    DBConnect() = delete;  
  
    DBConnect(const std::string& host, uint16_t port, const std::string& user, const std::string& password,  
              const std::string& name, const std::string& charset)  
        : m_host(host)  
        , m_user(user)  
        , m_password(password)  
        , m_name(name)  
        , m_charset(charset)  
        , m_port(port) {  
    }  
  
    ~DBConnect() {  
        this->close();  
    }  
  
    bool connect() {  
        MYSQL* conn = mysql_init(nullptr);  
        if (conn == nullptr) {  
            // LOG_ERROR("DBConnect::connect mysql start fail");  
            return false;  
        }  
  
        uint32_t timeout = 60;  
        mysql_options(conn, MYSQL_OPT_CONNECT_TIMEOUT, &timeout);  
        mysql_options(conn, MYSQL_OPT_READ_TIMEOUT, &timeout);  
        mysql_options(conn, MYSQL_OPT_WRITE_TIMEOUT, &timeout);  
  
        if (mysql_real_connect(conn, m_host.c_str(), m_user.c_str(), m_password.c_str(), m_name.c_str(), m_port,  
                               nullptr, 0) == nullptr) {  
            // LOG_ERROR("DBConnect::connect error:{}", mysql_error(conn));  
            return false;  
        }  
  
        if (mysql_set_character_set(conn, m_charset.c_str()) != 0) {  
            // LOG_ERROR("DBConnect::connect set character error:{}", mysql_error(conn));  
            mysql_close(conn);  
            return false;  
        }  
  
        this->close();  
        m_conn = conn;  
        return true;  
    }  
  
    MYSQL_RES* query(uint8_t type, uint64_t sn, std::string_view sql) {  
        if (mysql_real_query(m_conn, sql.data(), sql.length()) != 0) {  
            uint32_t error = mysql_errno(m_conn);  
            if (error == CR_COMMANDS_OUT_OF_SYNC || error == CR_SERVER_GONE_ERROR || error == CR_SERVER_LOST) {  
                std::size_t count = MYSQL_RECONNECT_COUNT;  
                while (count-- > 0) {  
                    if (this->connect()) {  
                        if (mysql_real_query(m_conn, sql.data(), sql.length()) == 0) {  
                            // LOG_DEBUG("DBConnect::query type:{} sn:{} sql:{}", type, sn, sql);  
                            return mysql_store_result(m_conn);  
                        }  
                        // LOG_ERROR("DBConnect::query still failed after type:{} sn:{} code:{} error:{} sql:{}", type,  
                        // sn,  
                        //           mysql_errno(m_conn), mysql_error(m_conn), sql);  
                        return nullptr;  
                    }  
  
                    std::this_thread::sleep_for(std::chrono::milliseconds(MYSQL_RECONNECT_TIMES));  
                }  
                // LOG_ERROR("DBConnect::query reopen faild type:{} sn:{} code:{} error:{} sql:{}", type, sn,  
                //           mysql_errno(m_conn), mysql_error(m_conn), sql);  
  
                return nullptr;  
            } else {  
                // LOG_ERROR("DBConnect::query type:{} sn:{} code:{} error:{} sql:{}", type, sn, mysql_errno(m_conn),  
                //           mysql_error(m_conn), sql);  
                return nullptr;  
            }  
        }  
  
        // LOG_DEBUG("DBConnect::query type:{} sn:{} sql:{}", type, sn, sql);  
        return mysql_store_result(m_conn);  
    }  
  
    void close() {  
        if (m_conn != nullptr) {  
            mysql_close(m_conn);  
            m_conn = nullptr;  
        }  
    }  
  
private:  
    MYSQL* m_conn{};  
    std::string m_host;  
    std::string m_user;  
    std::string m_password;  
    std::string m_name;  
    std::string m_charset;  
    uint16_t m_port;  
};  
  
using DBConnectPtr = std::shared_ptr<DBConnect>;  
  
#include <atomic>  
#include <iostream>  
#include <memory>  
#include <string>  
  
#include "concurrentqueue/blockingconcurrentqueue.h"  
  
class DBPool final {  
public:  
    DBPool() = default;  
  
    ~DBPool() {  
        stop();  
    }  
  
    bool start(const std::string& host, uint16_t port, const std::string& user, const std::string& password,  
               const std::string& name, const std::string& charset, uint16_t connCount) {  
        if (host.empty() || user.empty() || password.empty() || name.empty() || charset.empty() || connCount <= 0 ||  
            port <= 0) {  
            // LOG_ERROR("DBPool::start params error");  
            return false;  
        }  
  
        mysql_library_init(-1, nullptr, nullptr);  
  
        assert(mysql_thread_safe() == 1);  
  
        DBConnectPtr conn{};  
        for (std::size_t i = 0; i < connCount; ++i) {  
            conn = std::make_shared<DBConnect>(host, port, user, password, name, charset);  
            if (conn->connect()) {  
                post(conn);  
                m_running = true;  
            }  
        }  
  
        return m_running;  
    }  
  
    void stop() {  
        if (!m_running) {  
            return;  
        }  
  
        m_running = false;  
    }  
  
    DBConnectPtr pop() {  
        DBConnectPtr connect{};  
        while (m_running) {  
            if (!m_queue.wait_dequeue_timed(connect, std::chrono::milliseconds(1000))) {  
                continue;  
            }  
            break;  
        }  
  
        return connect;  
    }  
  
    void post(DBConnectPtr connect) {  
        m_queue.enqueue(connect);  
    }  
  
private:  
    moodycamel::BlockingConcurrentQueue<DBConnectPtr> m_queue{};  
    std::atomic_bool m_running{};  
};  
  
static int64_t getMillisecond() {  
    auto now = std::chrono::system_clock::now();  
    auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(now.time_since_epoch());  
    return duration.count();  
}  
  
int main() {  
    static boost::asio::thread_pool threadPool(8);  
  
    static auto gameDB = std::make_shared<DBPool>();  
  
    gameDB->start("127.0.0.1", 3306, "root", "123456", "boat", "utf8", 8);  
  
    static std::size_t startTime = getMillisecond();  
    static std::string sql = "SELECT VERSION()";  
    for (int i = 0; i < 10000; ++i) {  
        boost::asio::post(threadPool, []() {  
            auto connect = gameDB->pop();  
            if (!connect) {  
                return;  
            }  
  
            connect->query(0, 0, sql);  
            gameDB->post(connect);  
            static std::atomic_int count = 0;  
            if (++count >= 10000) {  
                std::cout << "=========use_time:" << getMillisecond() - startTime << std::endl;  
            }  
        });  
    }  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    threadPool.stop();  
    gameDB->stop();  
}  
```

---

## Comment 3

> Username: jphz  
> Created at: 2025-04-12 17:49:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2798925265  

mysql_version: 8.0.41  
The code I provided can be directly compiled, and I would like to identify any issues. Please assist me. Thank you!

---

## Comment 4

> Username: anarthal  
> Created at: 2025-04-13 16:44:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2800027353  

I've been playing with your code and doing some optimizations. Here's what I've found:  
  
Version 1: with coroutines, perform some optimizations:  
  
```cpp  
#include <boost/mysql/connection_pool.hpp>  
#include <boost/mysql/results.hpp>  
  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/bind_immediate_executor.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/redirect_error.hpp>  
#include <boost/asio/thread_pool.hpp>  
#include <boost/system/error_code.hpp>  
  
#include <chrono>  
#include <cstdint>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <string_view>  
#include <thread>  
  
using Callback = std::function<void(boost::system::error_code, boost::mysql::results& results)>;  
  
class DBPool final : public std::enable_shared_from_this<DBPool>  
{  
public:  
    DBPool() : m_threadPool(1) {}  
  
    ~DBPool() { stop(); }  
  
    bool start()  
    {  
        if (m_running)  
        {  
            return false;  
        }  
  
        m_running = true;  
  
        boost::mysql::pool_params params{};  
        params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
        params.username = "mytest";  
        params.password = "123456";  
        params.database = "boat";  
        params.ssl = static_cast<boost::mysql::ssl_mode>(0);  
        params.connect_timeout = std::chrono::seconds(0);  
  
        // Thread-safety adds extra overhead. Since everything is async, we can have  
        // as many connections as we want, with just once thread  
        params.thread_safe = false;  
  
        // Ensure we use exactly 8 connections, to match the libmysqlclient benchmark  
        params.initial_size = 8;  
        params.max_size = 8;  
  
        m_dbPool = std::make_shared<boost::mysql::connection_pool>(m_threadPool, std::move(params));  
        m_dbPool->async_run(boost::asio::detached);  
  
        // When async_run returns, connections are still connecting.  
        // Wait some time until sessions are established, so we don't  
        // count session establishment in the benchmark (we're not counting  
        // it in the libmysqlclient benchmark)  
        std::this_thread::sleep_for(std::chrono::milliseconds(200));  
  
        std::cout << "DBPool::start success" << std::endl;  
        return true;  
  
        return false;  
    }  
  
    void stop()  
    {  
        if (!m_running)  
        {  
            return;  
        }  
  
        m_running = false;  
  
        if (m_dbPool)  
        {  
            m_dbPool->cancel();  
            m_dbPool = nullptr;  
        }  
  
        m_threadPool.stop();  
        std::cout << "DBPool::stop success" << std::endl;  
    }  
  
    void query(std::string_view sql, Callback&& callback)  
    {  
        if (!m_dbPool)  
        {  
            return;  
        }  
  
        // Session(*m_dbPool, sql, std::move(callback)).start();  
  
        boost::asio::co_spawn(  
            m_dbPool->get_executor(),  
            [this, self = shared_from_this(), sql, callback = std::move(callback)](  
            ) -> boost::asio::awaitable<void> {  
                boost::system::error_code ec{};  
                boost::mysql::diagnostics errorInfo{};  
                boost::mysql::results results{};  
  
                // Use boost::asio::redirect_error(ec) instead of  
                // boost::asio::redirect_error(boost::asio::use_awaitable, ec). This allocates less memory,  
                // and is more efficient.  
                auto connection = co_await m_dbPool->async_get_connection(boost::asio::redirect_error(ec));  
                if (ec)  
                {  
                    callback(ec, results);  
                    co_return;  
                }  
  
                co_await connection->async_execute(sql, results, errorInfo, boost::asio::redirect_error(ec));  
                if (!ec)  
                {  
                    // By default, when a connection is returned to the pool, we call  
                    // the equivalent of async_reset_connection(), which rolls back open  
                    // transactions and cleans up any leftover state. If everything went well,  
                    // we can skip this step, which is costly because it implies talking to the server.  
                    // Only do this on success - if there was any error, this cleanup ensures correctness.  
                    // This is the most important change for performance.  
                    connection.return_without_reset();  
                }  
  
                callback(ec, results);  
            },  
            boost::asio::detached  
        );  
    }  
  
private:  
    bool m_running{};  
    std::shared_ptr<boost::mysql::connection_pool> m_dbPool{};  
    boost::asio::thread_pool m_threadPool{};  
};  
  
static int64_t getMillisecond()  
{  
    auto now = std::chrono::system_clock::now();  
    auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(now.time_since_epoch());  
    return duration.count();  
}  
  
int main()  
{  
    static auto gameDB = std::make_shared<DBPool>();  
    gameDB->start();  
    static std::size_t startTime = getMillisecond();  
    static std::string sql = "SELECT VERSION()";  
    for (int i = 0; i < 10000; ++i)  
    {  
        gameDB->query(sql, [](boost::system::error_code, boost::mysql::results&) {  
            static std::atomic_int count = 0;  
            if (++count >= 10000)  
            {  
                std::cout << "use_time:" << getMillisecond() - startTime << std::endl;  
            }  
        });  
    }  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    gameDB->stop();  
    return 0;  
}  
```  
  
Changes I've done:  
  
* Use `pooled_connection::return_without_reset` when you're done with connections. This prevents an extra round-trip to the server after connections are returned to the pool. This should only be done on success - the reset round-trip is a safety feature that will catch errors and clean up your connections if anything goes wrong. This is the change with the highest impact in performance.  
* Use only one thread in your thread pool, and disable thread-safe mode. Since `connection_pool` is asynchronous, you don't need more threads to get more parallelism (think of Node.js). Disabling thread-safety removes some internal re-scheduling that adds overhead.  
* You can set the number of connections to use by setting `initial_size` and `max_size`. I've set it to 8 to match the libmysqlclient example, but you can set any number there (limited by your server configuration). I've also added a sleep to wait until the connections are healthy before starting the benchmark, to make the benchmark fair.  
* You libmysqlclient benchmark is missing a `mysql_free_result`. I don't think it has a big impact in performance, but just in case.  
  
With this, on my machine, I get the following:  
  
```  
$> ./libmysqlclient_benchmark  
=========use_time:310  
=========use_time:311  
=========use_time:309  
$> ./boost_benchmark  
use_time:320  
use_time:324  
use_time:343  
```  
  
Version 2: alternatively, you can use callbacks instead of coroutines, which are slightly faster (although easier to get wrong):  
  
```cpp  
#include <boost/mysql/connection_pool.hpp>  
#include <boost/mysql/results.hpp>  
  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/bind_immediate_executor.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/redirect_error.hpp>  
#include <boost/asio/thread_pool.hpp>  
#include <boost/system/error_code.hpp>  
  
#include <chrono>  
#include <cstdint>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <string_view>  
#include <thread>  
  
using Callback = std::function<void(boost::system::error_code, boost::mysql::results& results)>;  
  
struct Session  
{  
    // Contains all variables that need to be kept alive until the final callback is called  
    struct impl  
    {  
        boost::mysql::connection_pool& pool;  
        std::string_view sql;  
        Callback callback;  
        boost::mysql::results result;  
        boost::mysql::pooled_connection conn;  
  
        impl(boost::mysql::connection_pool& pool, std::string_view sql, Callback&& cb)  
            : pool(pool), sql(sql), callback(std::move(cb))  
        {  
        }  
    };  
  
    std::unique_ptr<impl> pimpl;  
  
    Session(boost::mysql::connection_pool& pool, std::string_view sql, Callback&& cb)  
        : pimpl(new impl(pool, sql, std::move(cb)))  
    {  
    }  
  
    void start()  
    {  
        // Start running. First of all, enter the thread pool.  
        // Ever async_xxxx function needs to be called from the thread pool,  
        // or we will face race conditions.  
        auto* impl = pimpl.get();  
        boost::asio::dispatch(boost::asio::bind_executor(  
            impl->pool.get_executor(),  
            [self = std::move(*this)] mutable { self.on_dispatch(); }  
        ));  
    }  
  
    void on_dispatch()  
    {  
        // Running in the thread pool now. Get the connection  
        auto* impl = pimpl.get();  
        impl->pool.async_get_connection(  
            [self = std::move(*this)](boost::system::error_code ec, boost::mysql::pooled_connection c) mutable {  
                self.on_get_connection(ec, std::move(c));  
        });  
    }  
  
    void on_get_connection(boost::system::error_code ec, boost::mysql::pooled_connection conn)  
    {  
        if (ec)  
        {  
            pimpl->callback(ec, pimpl->result);  
            return;  
        }  
        auto& impl = *pimpl;  
  
        // Save conn in the state, so it's kept alive until we finish our operation.  
        // Otherwise, we will get use-after-free errors  
        impl.conn = std::move(conn);  
  
        // Start the execution  
        impl.conn->async_execute(  
            impl.sql,  
            impl.result,  
            [self = std::move(*this)](boost::system::error_code ec) mutable { self.on_execute(ec); }  
        );  
    }  
  
    void on_execute(boost::system::error_code ec)  
    {  
        // Done!  
        auto& impl = *pimpl;  
        impl.conn.return_without_reset();  
        impl.callback(ec, impl.result);  
    }  
};  
  
class DBPool final : public std::enable_shared_from_this<DBPool>  
{  
public:  
    DBPool() : m_threadPool(1) {}  
  
    ~DBPool() { stop(); }  
  
    bool start()  
    {  
        if (m_running)  
        {  
            return false;  
        }  
  
        m_running = true;  
  
        boost::mysql::pool_params params{};  
        params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
        params.username = "mytest";  
        params.password = "123456";  
        params.database = "boat";  
        params.ssl = static_cast<boost::mysql::ssl_mode>(0);  
        params.connect_timeout = std::chrono::seconds(0);  
  
        // Thread-safety adds extra overhead. Since everything is async, we can have  
        // as many connections as we want, with just once thread  
        params.thread_safe = false;  
  
        // Ensure we use exactly 8 connections, to match the libmysqlclient benchmark  
        params.initial_size = 8;  
        params.max_size = 8;  
  
        m_dbPool = std::make_shared<boost::mysql::connection_pool>(m_threadPool, std::move(params));  
        m_dbPool->async_run(boost::asio::detached);  
  
        // When async_run returns, connections are still connecting.  
        // Wait some time until sessions are established, so we don't  
        // count session establishment in the benchmark (we're not counting  
        // it in the libmysqlclient benchmark)  
        std::this_thread::sleep_for(std::chrono::milliseconds(200));  
  
        std::cout << "DBPool::start success" << std::endl;  
        return true;  
  
        return false;  
    }  
  
    void stop()  
    {  
        if (!m_running)  
        {  
            return;  
        }  
  
        m_running = false;  
  
        if (m_dbPool)  
        {  
            m_dbPool->cancel();  
            m_dbPool = nullptr;  
        }  
  
        m_threadPool.stop();  
        std::cout << "DBPool::stop success" << std::endl;  
    }  
  
    void query(std::string_view sql, Callback&& callback)  
    {  
        if (!m_dbPool)  
        {  
            return;  
        }  
  
        Session(*m_dbPool, sql, std::move(callback)).start();  
    }  
  
private:  
    bool m_running{};  
    std::shared_ptr<boost::mysql::connection_pool> m_dbPool{};  
    boost::asio::thread_pool m_threadPool{};  
};  
  
static int64_t getMillisecond()  
{  
    auto now = std::chrono::system_clock::now();  
    auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(now.time_since_epoch());  
    return duration.count();  
}  
  
int main()  
{  
    static auto gameDB = std::make_shared<DBPool>();  
    gameDB->start();  
    static std::size_t startTime = getMillisecond();  
    static std::string sql = "SELECT VERSION()";  
    for (int i = 0; i < 10000; ++i)  
    {  
        gameDB->query(sql, [](boost::system::error_code, boost::mysql::results&) {  
            static std::atomic_int count = 0;  
            if (++count >= 10000)  
            {  
                std::cout << "use_time:" << getMillisecond() - startTime << std::endl;  
            }  
        });  
    }  
  
    std::this_thread::sleep_for(std::chrono::seconds(100));  
    gameDB->stop();  
    return 0;  
}  
```  
  
With this code:  
  
```  
use_time:322  
use_time:313  
use_time:316  
```  
  
If you can get dedicated threads that just call the synchronous functions and nothing else, this is likely gonna be slightly faster than doing async. This is because async needs scheduling, saving callbacks, and so on, and this has a cost. Async is going to beat sync if you need to do I/O other than only the database. For instance, if you need to fetch any resource via HTTP, you don't need to spawn extra threads - you can use the same `asio::thread_pool`.  
  
If this difference in performance is unacceptable for you, you might also consider using the connection pool that you wrote, but using `any_connection` and sync functions.  
  
I've looked into the call graphs using `vtune` and didn't notice anything standing out. I can probably try to do small optimizations for Boost 1.89, but I don't promise a huge improvement.  
  
I hope this helps. If you have any other question, please let me know, I'm happy to help.  
  
Cheers,  
Ruben.

---

## Comment 5

> Username: jphz  
> Created at: 2025-04-14 02:14:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2800304444  

> I've been playing with your code and doing some optimizations. Here's what I've found:  
>   
> Version 1: with coroutines, perform some optimizations:  
>   
> #include <boost/mysql/connection_pool.hpp>  
> #include <boost/mysql/results.hpp>  
>   
> #include <boost/asio/awaitable.hpp>  
> #include <boost/asio/bind_executor.hpp>  
> #include <boost/asio/bind_immediate_executor.hpp>  
> #include <boost/asio/co_spawn.hpp>  
> #include <boost/asio/detached.hpp>  
> #include <boost/asio/dispatch.hpp>  
> #include <boost/asio/redirect_error.hpp>  
> #include <boost/asio/thread_pool.hpp>  
> #include <boost/system/error_code.hpp>  
>   
> #include <chrono>  
> #include <cstdint>  
> #include <functional>  
> #include <iostream>  
> #include <memory>  
> #include <string>  
> #include <string_view>  
> #include <thread>  
>   
> using Callback = std::function<void(boost::system::error_code, boost::mysql::results& results)>;  
>   
> class DBPool final : public std::enable_shared_from_this<DBPool>  
> {  
> public:  
>     DBPool() : m_threadPool(1) {}  
>   
>     ~DBPool() { stop(); }  
>   
>     bool start()  
>     {  
>         if (m_running)  
>         {  
>             return false;  
>         }  
>   
>         m_running = true;  
>   
>         boost::mysql::pool_params params{};  
>         params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
>         params.username = "mytest";  
>         params.password = "123456";  
>         params.database = "boat";  
>         params.ssl = static_cast<boost::mysql::ssl_mode>(0);  
>         params.connect_timeout = std::chrono::seconds(0);  
>   
>         // Thread-safety adds extra overhead. Since everything is async, we can have  
>         // as many connections as we want, with just once thread  
>         params.thread_safe = false;  
>   
>         // Ensure we use exactly 8 connections, to match the libmysqlclient benchmark  
>         params.initial_size = 8;  
>         params.max_size = 8;  
>   
>         m_dbPool = std::make_shared<boost::mysql::connection_pool>(m_threadPool, std::move(params));  
>         m_dbPool->async_run(boost::asio::detached);  
>   
>         // When async_run returns, connections are still connecting.  
>         // Wait some time until sessions are established, so we don't  
>         // count session establishment in the benchmark (we're not counting  
>         // it in the libmysqlclient benchmark)  
>         std::this_thread::sleep_for(std::chrono::milliseconds(200));  
>   
>         std::cout << "DBPool::start success" << std::endl;  
>         return true;  
>   
>         return false;  
>     }  
>   
>     void stop()  
>     {  
>         if (!m_running)  
>         {  
>             return;  
>         }  
>   
>         m_running = false;  
>   
>         if (m_dbPool)  
>         {  
>             m_dbPool->cancel();  
>             m_dbPool = nullptr;  
>         }  
>   
>         m_threadPool.stop();  
>         std::cout << "DBPool::stop success" << std::endl;  
>     }  
>   
>     void query(std::string_view sql, Callback&& callback)  
>     {  
>         if (!m_dbPool)  
>         {  
>             return;  
>         }  
>   
>         // Session(*m_dbPool, sql, std::move(callback)).start();  
>   
>         boost::asio::co_spawn(  
>             m_dbPool->get_executor(),  
>             [this, self = shared_from_this(), sql, callback = std::move(callback)](  
>             ) -> boost::asio::awaitable<void> {  
>                 boost::system::error_code ec{};  
>                 boost::mysql::diagnostics errorInfo{};  
>                 boost::mysql::results results{};  
>   
>                 // Use boost::asio::redirect_error(ec) instead of  
>                 // boost::asio::redirect_error(boost::asio::use_awaitable, ec). This allocates less memory,  
>                 // and is more efficient.  
>                 auto connection = co_await m_dbPool->async_get_connection(boost::asio::redirect_error(ec));  
>                 if (ec)  
>                 {  
>                     callback(ec, results);  
>                     co_return;  
>                 }  
>   
>                 co_await connection->async_execute(sql, results, errorInfo, boost::asio::redirect_error(ec));  
>                 if (!ec)  
>                 {  
>                     // By default, when a connection is returned to the pool, we call  
>                     // the equivalent of async_reset_connection(), which rolls back open  
>                     // transactions and cleans up any leftover state. If everything went well,  
>                     // we can skip this step, which is costly because it implies talking to the server.  
>                     // Only do this on success - if there was any error, this cleanup ensures correctness.  
>                     // This is the most important change for performance.  
>                     connection.return_without_reset();  
>                 }  
>   
>                 callback(ec, results);  
>             },  
>             boost::asio::detached  
>         );  
>     }  
>   
> private:  
>     bool m_running{};  
>     std::shared_ptr<boost::mysql::connection_pool> m_dbPool{};  
>     boost::asio::thread_pool m_threadPool{};  
> };  
>   
> static int64_t getMillisecond()  
> {  
>     auto now = std::chrono::system_clock::now();  
>     auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(now.time_since_epoch());  
>     return duration.count();  
> }  
>   
> int main()  
> {  
>     static auto gameDB = std::make_shared<DBPool>();  
>     gameDB->start();  
>     static std::size_t startTime = getMillisecond();  
>     static std::string sql = "SELECT VERSION()";  
>     for (int i = 0; i < 10000; ++i)  
>     {  
>         gameDB->query(sql, [](boost::system::error_code, boost::mysql::results&) {  
>             static std::atomic_int count = 0;  
>             if (++count >= 10000)  
>             {  
>                 std::cout << "use_time:" << getMillisecond() - startTime << std::endl;  
>             }  
>         });  
>     }  
>   
>     std::this_thread::sleep_for(std::chrono::seconds(100));  
>     gameDB->stop();  
>     return 0;  
> }  
> Changes I've done:  
>   
> * Use `pooled_connection::return_without_reset` when you're done with connections. This prevents an extra round-trip to the server after connections are returned to the pool. This should only be done on success - the reset round-trip is a safety feature that will catch errors and clean up your connections if anything goes wrong. This is the change with the highest impact in performance.  
> * Use only one thread in your thread pool, and disable thread-safe mode. Since `connection_pool` is asynchronous, you don't need more threads to get more parallelism (think of Node.js). Disabling thread-safety removes some internal re-scheduling that adds overhead.  
> * You can set the number of connections to use by setting `initial_size` and `max_size`. I've set it to 8 to match the libmysqlclient example, but you can set any number there (limited by your server configuration). I've also added a sleep to wait until the connections are healthy before starting the benchmark, to make the benchmark fair.  
> * You libmysqlclient benchmark is missing a `mysql_free_result`. I don't think it has a big impact in performance, but just in case.  
>   
> With this, on my machine, I get the following:  
>   
> ```  
> $> ./libmysqlclient_benchmark  
> =========use_time:310  
> =========use_time:311  
> =========use_time:309  
> $> ./boost_benchmark  
> use_time:320  
> use_time:324  
> use_time:343  
> ```  
>   
> Version 2: alternatively, you can use callbacks instead of coroutines, which are slightly faster (although easier to get wrong):  
>   
> #include <boost/mysql/connection_pool.hpp>  
> #include <boost/mysql/results.hpp>  
>   
> #include <boost/asio/awaitable.hpp>  
> #include <boost/asio/bind_executor.hpp>  
> #include <boost/asio/bind_immediate_executor.hpp>  
> #include <boost/asio/co_spawn.hpp>  
> #include <boost/asio/detached.hpp>  
> #include <boost/asio/dispatch.hpp>  
> #include <boost/asio/redirect_error.hpp>  
> #include <boost/asio/thread_pool.hpp>  
> #include <boost/system/error_code.hpp>  
>   
> #include <chrono>  
> #include <cstdint>  
> #include <functional>  
> #include <iostream>  
> #include <memory>  
> #include <string>  
> #include <string_view>  
> #include <thread>  
>   
> using Callback = std::function<void(boost::system::error_code, boost::mysql::results& results)>;  
>   
> struct Session  
> {  
>     // Contains all variables that need to be kept alive until the final callback is called  
>     struct impl  
>     {  
>         boost::mysql::connection_pool& pool;  
>         std::string_view sql;  
>         Callback callback;  
>         boost::mysql::results result;  
>         boost::mysql::pooled_connection conn;  
>   
>         impl(boost::mysql::connection_pool& pool, std::string_view sql, Callback&& cb)  
>             : pool(pool), sql(sql), callback(std::move(cb))  
>         {  
>         }  
>     };  
>   
>     std::unique_ptr<impl> pimpl;  
>   
>     Session(boost::mysql::connection_pool& pool, std::string_view sql, Callback&& cb)  
>         : pimpl(new impl(pool, sql, std::move(cb)))  
>     {  
>     }  
>   
>     void start()  
>     {  
>         // Start running. First of all, enter the thread pool.  
>         // Ever async_xxxx function needs to be called from the thread pool,  
>         // or we will face race conditions.  
>         auto* impl = pimpl.get();  
>         boost::asio::dispatch(boost::asio::bind_executor(  
>             impl->pool.get_executor(),  
>             [self = std::move(*this)] mutable { self.on_dispatch(); }  
>         ));  
>     }  
>   
>     void on_dispatch()  
>     {  
>         // Running in the thread pool now. Get the connection  
>         auto* impl = pimpl.get();  
>         impl->pool.async_get_connection(  
>             [self = std::move(*this)](boost::system::error_code ec, boost::mysql::pooled_connection c) mutable {  
>                 self.on_get_connection(ec, std::move(c));  
>         });  
>     }  
>   
>     void on_get_connection(boost::system::error_code ec, boost::mysql::pooled_connection conn)  
>     {  
>         if (ec)  
>         {  
>             pimpl->callback(ec, pimpl->result);  
>             return;  
>         }  
>         auto& impl = *pimpl;  
>   
>         // Save conn in the state, so it's kept alive until we finish our operation.  
>         // Otherwise, we will get use-after-free errors  
>         impl.conn = std::move(conn);  
>   
>         // Start the execution  
>         impl.conn->async_execute(  
>             impl.sql,  
>             impl.result,  
>             [self = std::move(*this)](boost::system::error_code ec) mutable { self.on_execute(ec); }  
>         );  
>     }  
>   
>     void on_execute(boost::system::error_code ec)  
>     {  
>         // Done!  
>         auto& impl = *pimpl;  
>         impl.conn.return_without_reset();  
>         impl.callback(ec, impl.result);  
>     }  
> };  
>   
> class DBPool final : public std::enable_shared_from_this<DBPool>  
> {  
> public:  
>     DBPool() : m_threadPool(1) {}  
>   
>     ~DBPool() { stop(); }  
>   
>     bool start()  
>     {  
>         if (m_running)  
>         {  
>             return false;  
>         }  
>   
>         m_running = true;  
>   
>         boost::mysql::pool_params params{};  
>         params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
>         params.username = "mytest";  
>         params.password = "123456";  
>         params.database = "boat";  
>         params.ssl = static_cast<boost::mysql::ssl_mode>(0);  
>         params.connect_timeout = std::chrono::seconds(0);  
>   
>         // Thread-safety adds extra overhead. Since everything is async, we can have  
>         // as many connections as we want, with just once thread  
>         params.thread_safe = false;  
>   
>         // Ensure we use exactly 8 connections, to match the libmysqlclient benchmark  
>         params.initial_size = 8;  
>         params.max_size = 8;  
>   
>         m_dbPool = std::make_shared<boost::mysql::connection_pool>(m_threadPool, std::move(params));  
>         m_dbPool->async_run(boost::asio::detached);  
>   
>         // When async_run returns, connections are still connecting.  
>         // Wait some time until sessions are established, so we don't  
>         // count session establishment in the benchmark (we're not counting  
>         // it in the libmysqlclient benchmark)  
>         std::this_thread::sleep_for(std::chrono::milliseconds(200));  
>   
>         std::cout << "DBPool::start success" << std::endl;  
>         return true;  
>   
>         return false;  
>     }  
>   
>     void stop()  
>     {  
>         if (!m_running)  
>         {  
>             return;  
>         }  
>   
>         m_running = false;  
>   
>         if (m_dbPool)  
>         {  
>             m_dbPool->cancel();  
>             m_dbPool = nullptr;  
>         }  
>   
>         m_threadPool.stop();  
>         std::cout << "DBPool::stop success" << std::endl;  
>     }  
>   
>     void query(std::string_view sql, Callback&& callback)  
>     {  
>         if (!m_dbPool)  
>         {  
>             return;  
>         }  
>   
>         Session(*m_dbPool, sql, std::move(callback)).start();  
>     }  
>   
> private:  
>     bool m_running{};  
>     std::shared_ptr<boost::mysql::connection_pool> m_dbPool{};  
>     boost::asio::thread_pool m_threadPool{};  
> };  
>   
> static int64_t getMillisecond()  
> {  
>     auto now = std::chrono::system_clock::now();  
>     auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(now.time_since_epoch());  
>     return duration.count();  
> }  
>   
> int main()  
> {  
>     static auto gameDB = std::make_shared<DBPool>();  
>     gameDB->start();  
>     static std::size_t startTime = getMillisecond();  
>     static std::string sql = "SELECT VERSION()";  
>     for (int i = 0; i < 10000; ++i)  
>     {  
>         gameDB->query(sql, [](boost::system::error_code, boost::mysql::results&) {  
>             static std::atomic_int count = 0;  
>             if (++count >= 10000)  
>             {  
>                 std::cout << "use_time:" << getMillisecond() - startTime << std::endl;  
>             }  
>         });  
>     }  
>   
>     std::this_thread::sleep_for(std::chrono::seconds(100));  
>     gameDB->stop();  
>     return 0;  
> }  
> With this code:  
>   
> ```  
> use_time:322  
> use_time:313  
> use_time:316  
> ```  
>   
> If you can get dedicated threads that just call the synchronous functions and nothing else, this is likely gonna be slightly faster than doing async. This is because async needs scheduling, saving callbacks, and so on, and this has a cost. Async is going to beat sync if you need to do I/O other than only the database. For instance, if you need to fetch any resource via HTTP, you don't need to spawn extra threads - you can use the same `asio::thread_pool`.  
>   
> If this difference in performance is unacceptable for you, you might also consider using the connection pool that you wrote, but using `any_connection` and sync functions.  
>   
> I've looked into the call graphs using `vtune` and didn't notice anything standing out. I can probably try to do small optimizations for Boost 1.89, but I don't promise a huge improvement.  
>   
> I hope this helps. If you have any other question, please let me know, I'm happy to help.  
>   
> Cheers, Ruben.  
  
Thank you very much for your help.  
After your optimization, it was found that in single threaded testing, the performance is indeed higher than libmysqlclient, but there is still a significant gap in performance in multi-threaded testing.  
Single threaded:  
libmysqlclient: 1584ms  
boost.mysql: 909ms  
8 threads:  
libmysqlclient: 276ms  
boost.mysql: 2449ms  
2 threads:  
libmysqlclient: 803ms  
boost.mysql: 988ms

---

## Comment 6

> Username: anarthal  
> Created at: 2025-04-14 19:06:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2802721606  

Yes, there will always be, because asynchronous code doesn't necessarily increase throughput with more threads, while sync code does (because that's the only way it has to achieve concurrency). You need to compare the 8 thread value figure in libmysqlclient with the single-thread figure in the async version. In my measurements, they were pretty close - with the async version being around 5% slower.  
  
Remember that even if you make your `asio::thread_pool` single-threaded, your application can still have as many threads as you want, as `asio::post` / `asio::dispatch` are thread-safe.

---

## Comment 7

> Username: jphz  
> Created at: 2025-04-15 01:12:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2803469791  

> 是的，总会有，因为异步代码不一定会随着线程的增加而增加吞吐量，而同步代码会（因为这是它必须实现并发的唯一方式）。您需要将 libmysqlclient 中的 8 线程值数字与异步版本中的单线程数字进行比较。在我的测量中，它们非常接近 - 异步版本慢了大约 5%。  
>   
> 请记住，即使你把你的 clone 设为单线程，你的应用程序仍然可以有你想要的任意数量的线程，因为 / 是线程安全的。`asio::thread_pool``asio::post``asio::dispatch`  
  
Thank you very much for your answer.

---

## Comment 8

> Username: anarthal  
> Created at: 2025-04-15 16:57:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/467#issuecomment-2806869687  

If you are okay with it, I will be closing this issue. Feel free to open another one if you find any other difficulty.  
  
Cheers,  
Ruben.
