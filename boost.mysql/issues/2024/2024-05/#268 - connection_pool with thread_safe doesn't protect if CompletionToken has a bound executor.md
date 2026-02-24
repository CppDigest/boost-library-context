# #268 - connection_pool with thread_safe doesn't protect if CompletionToken has a bound executor [Closed]

> Username: Chrys4lisfag  
> Created at: 2024-05-18 12:01:33 UTC  
> Updated at: 2024-09-14 11:06:55 UTC  
> Closed at: 2024-09-14 11:06:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/268  

Is it safe to run mysql operation when using thread pool within another io context?  
  
Currently I run all operation within my main iocontext which is separate from mysql ctx and I get unhandled exceptions quite often.  
  
Judging by the exception (access violation) address it is somewhere here. Which looks like thread safety issue happening between .empty() and .front() operations.  
  
https://github.com/boostorg/mysql/blob/1b6a908ba9dc260f24d100e8417096c1ee393c14/include/boost/mysql/impl/internal/connection_pool/connection_pool_impl.hpp#L220  
  
My mysql setup ctx.  
```  
boost::asio::thread_pool mysql_pool( 4 );  
auto shared_state = std::make_shared<boost::mysql::connection_pool>(  
	boost::mysql::pool_executor_params::thread_safe( mysql_pool.get_executor() ),  
	std::move( mysql_pool_config )  
);  
  
shared_state->async_run( boost::asio::detached );  
  
// main ioctx used by the server itself running on some threads  
boost::asio::io_context io_context;  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2024-05-18 13:14:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/268#issuecomment-2118822691  

The intent is that what you are trying should be safe, but I think there is a problem in the code when the completion token you pass to `async_get_connection` has a bound executor. Could you please post how are you calling `async_get_connection`?  
  
On the other hand, what's the use case behind using a separate thread pool for the MySQL connection pool? There's a high chance that using separate thread pools will make your code less efficient. `connection_pool` doesn't perform any blocking operations, so it doesn't require dedicated threads (it's all async calls in the inside).

---

## Comment 2

> Username: Chrys4lisfag  
> Created at: 2024-05-18 13:44:24 UTC  
> Updated at: 2024-05-18 13:45:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/268#issuecomment-2118830129  

I understand that it has no sense in separating them. It has been done as a temp solution to test out if things are working correctly after switching to internal pools.  
  
  
The method is called from the server listener ctx  
```  
boost::asio::awaitable<bool> session_db_remote::contains( const std::string& string )  
{  
	try  
	{  
		auto con = co_await _pool->async_get_connection( boost::asio::use_awaitable );  
		const auto find_sql = boost::mysql::format_sql( con->format_opts().value(),  
						"SELECT 1 FROM backups WHERE access_token = {}",  
			string );  
  
		boost::mysql::results result;  
		co_await con->async_execute( find_sql, result, boost::asio::use_awaitable );  
  
		co_return !result.rows().empty();  
	}  
	MYSQL_CATCH_BLOCK_ASYNC( "db::contains" )  
}  
```

---

## Comment 3

> Username: Chrys4lisfag  
> Created at: 2024-05-18 14:25:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/268#issuecomment-2118840733  

Moved to a single context. Will try running under the debugger if any unhandled exception occurs.

---

## Comment 4

> Username: anarthal  
> Created at: 2024-05-18 15:03:45 UTC  
> Url: https://github.com/boostorg/mysql/issues/268#issuecomment-2118850949  

That confirms my theory. For reference, the problem is:  
  
* When you create a `connection_pool` with the `thread_safe` function, the pool gets created with an `asio::strand` wrapping the executor you pass, in your case the `thread_pool` executor.  
* When you `co_await async_get_connection(..., asio::use_awaitable)`, the completion token has an implicitly bound executor: the one that you used for `co_spawn` - this would be your server's `io_context` executor.  
* [This line](https://github.com/boostorg/mysql/blob/1b6a908ba9dc260f24d100e8417096c1ee393c14/include/boost/mysql/impl/internal/connection_pool/connection_pool_impl.hpp#L198) in `async_get_connection` attempts to get to run your operation within the `strand` (`obj_->ex_` is the strand here).  
* `asio::post(obj_->ex_, std::move(self))` posts to `obj_->ex_`, then dispatches to  `get_associated_executor(self, obj_->ex_)` (post to the strand, then dispatch to the server's I/O context). As the strand's underlying context is different to the server's I/O context, this effectively bypasses the strand.  
  
You should be fine if:  
* You run everything using the same `thread_pool`.  
* You use `co_await _pool->async_get_connection(asio::bind_executor(_pool->get_executor(), asio::use_awaitable));`  
  
I don't know if there is a portable way to fix this to make it work in the general case (`asio::post` behavior is non-obvious even for me). We should, ideally, fix it. Otherwise, document this, as it's non-obvious behavior.  
  
Please let me know if you find more trouble or have any other questions.

---

## Comment 5

> Username: Chrys4lisfag  
> Created at: 2024-05-18 19:49:17 UTC  
> Updated at: 2024-05-18 19:49:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/268#issuecomment-2118988107  

No more issues in a single context as expected.  
Not sure whether you should tag this behavior as a bug, as it is actually very logical behavior.  
  
Maybe just add a "Thread Safety" article in the README specifying that you must either use a single context or use the pool executor for MySQL operations.
