# #487 - Boost::Mysql async_get_connection timeout [Closed]

> Username: atidot3  
> Created at: 2025-06-19 13:19:09 UTC  
> Updated at: 2025-06-20 07:39:30 UTC  
> Closed at: 2025-06-20 07:39:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/487  

Hello, i've made a custom class that store and handle my connection_pool using 1.88.  
After several testing and reading i've noticed the pool never trigger the connection timeout,  
My mariadb server is turn off trying to aquired connection using the pool and never get anything.  
  
Some code:  
  
Aquire connection:  
  
> async_result<boost::mysql::pooled_connection> connection_pool_wrapper::async_get_connection() noexcept  
> {  
> 	boost::mysql::diagnostics diags;  
>   
> 	//stuck here until ctrl-c the program  
> 	auto [ec, connection] = co_await _pool->async_get_connection(diags, tuple_awaitable_token);  
> 	if(ec)  
>     {  
>         co_return std::unexpected{boost::mysql::error_with_diagnostics(ec, diags)};  
>     }  
>   
> 	//return the connection  
> 	co_return std::move(connection);  
> }  
  
  
Initiate params and pool  
  
> void connection_pool_wrapper::start(connection_pool_wrapper_options options) noexcept  
> {  
> 	// -- pool options  
> 	boost::mysql::pool_params param_opts;  
> 	param_opts.server_address = boost::mysql::host_and_port{options.hostname, options.port};  
> 	param_opts.username = options.username;  
> 	param_opts.password = options.password;  
> 	param_opts.database = options.database;  
> 	param_opts.ssl = boost::mysql::ssl_mode::enable;  
> 	param_opts.multi_queries = false;  
> 	param_opts.initial_size = std::size_t(5);  
> 	param_opts.thread_safe = true;  
>         param_opts.connection_executor = _exec;  
>   
> 	//construct the pool from options  
> 	_pool = std::make_unique<boost::mysql::connection_pool>(_exec, std::move(param_opts));  
>   
> 	//run the pool asynchronously  
> 	_pool->async_run(boost::asio::detached);  
> }

---

## Comment 1

> Username: anarthal  
> Created at: 2025-06-19 17:58:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/487#issuecomment-2988815976  

Hi @atidot3,  
  
The behavior you're seeing is expected. See [this section](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/connection_pool.html#mysql.connection_pool.applying_a_timeout_to_async_get_) of the docs. `async_get_connection` will by default block indefinitely, until a connection is available.  
  
The way to prevent this is using the `asio::cancel_after` completion token, which can be used with any async operation to set a timeout:  
  
```cpp  
using namespace std::chrono_literals;  
boost::mysql::diagnostics diags;  
  
// This will fail if a connection is not available in 30s. diag will be populated with info about  
// the last connection attempt, if any  
auto [ec, connection] = co_await _pool->async_get_connection(diags, asio::cancel_after(30s, asio::as_tuple));  
if(ec)  
{  
co_return std::unexpected{boost::mysql::error_with_diagnostics(ec, diags)};  
}  
  
//return the connection  
co_return std::move(connection);  
```  
  
Note that, if you're in a multi-threaded environment (which I think you are, since you're setting `pool_params::thread_safe` to `true`), you need to run the coroutine that's calling `async_get_connection` within a strand. `asio::cancel_after` launches a timer in parallel to `async_get_connection`, and that can result in data races without the strand (the timer is outside of what the pool can control).  
  
Hope this helps. If you have any more questions, please let me know.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: atidot3  
> Created at: 2025-06-19 18:36:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/487#issuecomment-2988874435  

Hey thanks for the answer,   
I tried to use asio::cancel_after with a 5s timer and faced the exact same issue thats why i've been reaching to the issues, i didnt provided everthing i tried sorry.  
  
And yes i'm using multi-threaded env, im using boost::asio::thread_pool to manage every coro in the program. (set to cpu count*2)  
  
To give you more context,  
We start the program and immediatly create our sql_wrapper and start the pool then try to gather some data from the database,  
Everything is working fine as long as the sql server is available at start or late launch.  
I'm only facing this issue as long as the sql server is shut.  
  
I'm out of work atm so i'll try to reproduce this issue on a separated program and let you know :)

---

## Comment 3

> Username: anarthal  
> Created at: 2025-06-19 18:56:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/487#issuecomment-2988911624  

Hm, then that's weird. Just to confirm, is your coroutine running within a strand?  
  
If you manage to reproduce the problem, or somehow provide me with a way to do it, I'd be thankful.  
  
Cheers,  
Rubén.

---

## Comment 4

> Username: atidot3  
> Created at: 2025-06-20 07:39:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/487#issuecomment-2990139685  

Hey,  
  
After settingup a boost_test i can confirm its working as intended with native class,  
Now i have to determine why its not with our 'wrapper' since the code is the exact same...  
Thanks for your time :)  
  
Ati.
