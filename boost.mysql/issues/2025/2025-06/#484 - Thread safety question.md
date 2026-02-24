# #484 - Thread safety question [Closed]

> Username: Chrys4lisfag  
> Created at: 2025-06-13 16:40:33 UTC  
> Updated at: 2025-06-13 17:21:42 UTC  
> Closed at: 2025-06-13 17:21:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/484  

Hi, everyone! Maybe this is more like general asio question but I want to ask about thread safety of some operation when running mysql client in multithreaded(threadsafe mode).  
  
Here is some code. When I run operation in some other from mysql exec context (like tcp socket) should I specify proper executor for the completion token or any would be ok?  
  
As I understand, all mysql internal operations are thread safe and guarded by strand but is it ok for the completion handler to use token binded to external executor?  
  
I appreciate anyone explaining me this question! Thanks!  
  
```  
  
boost::asio::awaitable<bool> session_db::find_session(const std::string& session_id)  
{  
    try {  
         
  
	auto con = co_await _pool->async_get_connection( boost::asio::deferred );  
        // or should I do this this?   
        auto con2 = co_await _pool->async_get_connection( boost::bind_executor( _pool->get_executor(), boost::asio::deferred ) );  
  
        const auto stm = boost::mysql::format_sql(con->format_opts().value(), "SELECT EXISTS(SELECT 1 FROM backups WHERE access_token = {})", session_id );  
  
        boost::mysql::results result;  
	co_await con->async_execute( find_sql, result, boost::asio::deferred );  
        // or this? (or use con executor, guess does not matter)  
        co_await con->async_execute( stm, result, boost::bind_executor( _pool->get_executor(), boost::asio::deferred ) );  
	con.return_without_reset();  
  
	co_return result.rows().at(0).at(0).as_int64() == 1;  
  
    } catch (...) {  
        // Handle exceptions here  
        co_return false;  
    }  
  
}  
  
  
boost::asio::awaitable<void> function_running_on_tcp_socket_executor()  
{  
    const auto exists = co_await session_db::find_session("some_session_id");  
}  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2025-06-13 17:12:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/484#issuecomment-2970999285  

Hi @Chrys4lisfag,  
  
Individual connectors are not thread-safe. This means that if you share them between threads, you will have trouble. However, the MySQL protocol is half-duplex (like HTTP: you write a request, then wait for a response). This means that this is safe:  
  
```cpp  
void do_session(mysql::any_connection& conn) {  
    const auto stm = boost::mysql::format_sql(con->format_opts().value(), "SELECT EXISTS(SELECT 1 FROM backups WHERE access_token = {})", session_id );  
   mysql::results r;  
   co_await conn.async_execute(stm, r);  
   // Do whatever you need with r  
}  
```  
  
This is safe even if you're running using an `asio::thread_pool` or an `asio::io_context` with multiple threads calling run(). It is safe as long as you don't have another coroutine running in parallel using the same connection. This is usually the case, since sharing a single connection between sessions results in problems.  
  
The `connection_pool` is a little bit different, because you usually create a single `connection_pool` per application and share it between all the sessions, coroutines or tasks that your application is executing. By default, `connection_pool` does **not** do any strand dispatching, but it can be configured to do so by creating it with a `pool_params` object that sets `thread_safe=true`.  
  
TL;DR: your code should be safe like this:  
  
```cpp  
boost::asio::awaitable<bool> session_db::find_session(const std::string& session_id)  
{  
    try {  
         
        // This one is safe as long as you created the connection_pool with thread_safe=true  
	auto con = co_await _pool->async_get_connection( boost::asio::deferred );  
  
        const auto stm = boost::mysql::format_sql(con->format_opts().value(), "SELECT EXISTS(SELECT 1 FROM backups WHERE access_token = {})", session_id );  
  
        boost::mysql::results result;  
        // This one is safe because you're not sharing the connection with any other coroutine  
	co_await con->async_execute( find_sql, result, boost::asio::deferred );  
	con.return_without_reset();  
  
	co_return result.rows().at(0).at(0).as_int64() == 1;  
  
    } catch (...) {  
        // Handle exceptions here  
        co_return false;  
    }  
  
}  
  
  
boost::asio::awaitable<void> function_running_on_tcp_socket_executor()  
{  
    const auto exists = co_await session_db::find_session("some_session_id");  
}  
```  
  
Note that, if you're using the latest Boost version, you can rewrite your code as:  
  
```cpp  
boost::asio::awaitable<bool> session_db::find_session(const std::string& session_id)  
{  
    try {  
       
	auto con = co_await _pool->async_get_connection();  
        boost::mysql::results<std::tuple<std::int64_T>> result;  
	co_await con->async_execute(boost::mysql::with_params("SELECT EXISTS(SELECT 1 FROM backups WHERE access_token = {})", session_id), result);  
	con.return_without_reset();  
  
	co_return !result.rows().empty() && std::get<0>(result.rows()[0]);  
  
    } catch (...) {  
        // Handle exceptions here  
        co_return false;  
    }  
  
}  
  
  
boost::asio::awaitable<void> function_running_on_tcp_socket_executor()  
{  
    const auto exists = co_await session_db::find_session("some_session_id");  
}  
```  
  
Special conditions apply if you use the `asio::cancel_after` completion token to specify timeouts (which you should probably do, at least for `async_get_connection`, since the function waits by default indefinitely until a connection is ready). `asio::cancel_after` launches a timer in parallel, and that can create data races. If `function_running_on_tcp_socket_executor` is running on a strand, then you're safe and you don't need to do anything. If it's not, then you'd need to wrap your code within a strand. This formula could work for you:  
  
```cpp  
boost::asio::awaitable<bool> session_db::find_session(const std::string& session_id)  
{  
    try {  
       
	auto con = co_await _pool->async_get_connection(boost::asio::cancel_after(30s));  
        boost::mysql::results<std::tuple<std::int64_T>> result;  
	co_await con->async_execute(boost::mysql::with_params("SELECT EXISTS(SELECT 1 FROM backups WHERE access_token = {})", session_id), result);  
	con.return_without_reset();  
  
	co_return !result.rows().empty() && std::get<0>(result.rows()[0]);  
  
    } catch (...) {  
        // Handle exceptions here  
        co_return false;  
    }  
  
}  
  
  
boost::asio::awaitable<void> function_running_on_tcp_socket_executor()  
{  
    // spawn a coroutine that runs within the strand. This protects against any possible  
    // internal race conditions caused by the coroutine and the timer running in parallel  
    const auto exists = co_await boost::asio::co_spawn(  
        boost::asio::make_strand(co_await boost::asio::this_coro::executor), // executor to spawn the coroutine on  
        session_db::find_session("some_session_id") // what to run  
        // not passing any completion token is equal to passing asio::deferred  
    );  
}  
```  
  
Disclaimer: I haven't run the code, so it can contain mistakes.  
  
I'd advise to measure efficiency (strand dispatching has a cost) and run your code with the `-fsanitize=thread` flag to be completely sure. Thread-safety in Asio has many pointy edges.  
  
Let me know if this solves your question.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: Chrys4lisfag  
> Created at: 2025-06-13 17:21:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/484#issuecomment-2971017252  

Thank you for the detailed answer. It completely answers my question. There are also some good tips you pointed out
