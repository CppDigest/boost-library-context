# #343 - Is there any way to interrupt the current query of a pooled connection? [Closed]

> Username: LucianZz  
> Created at: 2024-09-10 08:30:41 UTC  
> Updated at: 2026-01-23 09:45:00 UTC  
> Closed at: 2024-09-13 12:50:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/343  

Is there any way to interrupt the current query of a pooled connection?

---

## Comment 1

> Username: anarthal  
> Created at: 2024-09-10 15:28:09 UTC  
> Updated at: 2024-09-10 15:28:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/343#issuecomment-2341258394  

Hi @LucianZz,  
  
Yes, you can interrupt any asynchronous operation using the standard Asio mechanisms. Recall that calling `conn->async_execute(...)` on a `pooled_connection` object actually calls [`any_connection::async_execute`](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__any_connection/async_execute.html).  
  
Cancelling an operation in Asio can be done, for instance, using `asio::experimental::parallel_group`. For instance, this will cancel the query if it doesn't complete in 5 seconds (I'm using C++20 coroutines because it's simpler - please let me know if you need another completion style):  
  
```cpp  
namespace asio = boost::asio;  
namespace mysql = boost::mysql;  
  
// Run a query with a timeout of 5.  
asio::awaitable<void> interrupt_query_after_5s(mysql::pooled_connection& conn)  
{  
    // Required to run async_execute  
    mysql::results r;  
  
    // A timer object, to wait 5s  
    asio::steady_timer tim(conn->get_executor());  
    tim.expires_after(std::chrono::seconds(5));  
  
    // clang-format off  
    // Launch the query and the timer wait, in parallel. wait_for_one() will   
    // wait for the operation that completes first, and then cancel the other one  
    // (similar to Promise.race in Javascript).  
    // compl_order is a std::array<std::size_t, 2> with the order in with the ops completed.  
    //     execute completed first: {0, 1}  
    //     timer completed first: {1, 0}  
    // execute_err is the error code produced by the query  
    // timer_err is the error code produced by the timer wait  
    auto [compl_order, execute_err, timer_err] = co_await asio::experimental::make_parallel_group(  
        conn->async_execute("SELECT 1", r, asio::deferred),  
        tim.async_wait(asio::deferred)  
    ).async_wait(asio::experimental::wait_for_one(), asio::deferred);  
    // clang-format on  
  
    // Check whether the operation completed successfully  
    if (compl_order[0] == 1u) {  
        // timeout  
    } else if (execute_err) {  
        // error running execute  
    } else {  
        // success  
    }  
}  
```  
  
Note that only async operations can be interrupted. If a query gets interrupted, the protocol exposes no way to know whether the server actually processed it or not. The interruption leaves connection in an unknown state, so after a timeout, you shouldn't use them further. After you return them to the pool (by letting the `pooled_connection` object go out of scope), the pool will take care of reconnecting them again if required.  
  
Does this answer your question? I'm happy to provide further guidance if you provide more details on your use case.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: LucianZz  
> Created at: 2024-09-11 06:21:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/343#issuecomment-2342718836  

Thank you for your reply. My need is to immediately interrupt the current query of a pooled connection, so my current approach is to obtain the CONNECTION_ID through "SELECT CONNECTION_ID()", and then interrupt the query through "KILL QUERY"

---

## Comment 3

> Username: anarthal  
> Created at: 2024-09-11 10:42:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/343#issuecomment-2343285502  

That's an option, but you will need a separate connection to issue the `KILL QUERY` statement, as the server processes queries in the same connection sequentially. According to MySQL documentation, using this approach will leave your original connection intact.  
  
You need to obtain the connection ID before issuing the query, then pass the ID to the connection you're using to issue the `KILL QUERY` command. In principle, this should work. Without knowing your application architecture, these are some problems I can think of:  
  
- You want to interrupt a query, but all connections are busy running heavyweight queries, so your cancellation ends up being delivered late.  
- Your cancellation is delivered after the query finishes, and ends up interrupting the next query.  
  
I can't evaluate whether these are relevant or not without knowing more.  
  
The alternative would be using a `parallel_group` with a second task that waits for some sort of cancellation signal to arrive, like this:  
  
```cpp  
// Task handling cancellations  
asio::awaitable<void> wait_for_cancellation()  
{  
    // Wait for a cancellation signal to arrive. This depends on how  
    // your application knows that the query should be cancelled -  
    // maybe reading from a socket, receiving an HTTP request, a signal from a parent process...  
    co_await something_specific_to_your_app();  
}  
  
asio::awaitable<void> interruptable_query(mysql::pooled_connection& conn)  
{  
    // Required to run async_execute  
    mysql::results r;  
  
    // Launch the query and the task that listens to the cancellation, in parallel. wait_for_one() will   
    // wait for the operation that completes first, and then cancel the other one  
    // (similar to Promise.race in Javascript).  
    // compl_order is a std::array<std::size_t, 2> with the order in with the ops completed.  
    //     execute completed first: {0, 1}  
    //     wait_for_cancellation wait completed first: {1, 0}  
    // execute_err is the error code produced by the query  
    // wait_for_cancellation_err is a std::exception_ptr produced by wait_for_cancellation  
    auto [compl_order, execute_err, wait_for_cancellation_err] = co_await asio::experimental::make_parallel_group(  
        conn->async_execute("SELECT 1", r, asio::deferred),  
        asio::co_spawn(  
            co_await asio::this_coro::executor,  
            &wait_for_cancellation,  
            asio::deferred  
        )  
    ).async_wait(asio::experimental::wait_for_one(), asio::deferred);  
  
    // Check whether the operation completed successfully  
    if (execute_err)  
    {  
        // We have an error. asio::error::operation_aborted means that the query was cancelled  
    }  
}  
```  
  
The viability of this approach depends on whether writing `something_specific_to_your_app` is doable or not.  
  
Let me know if I can help more.  
  
Regards,  
Ruben.

---

## Comment 4

> Username: anarthal  
> Created at: 2024-09-13 12:50:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/343#issuecomment-2348884630  

If you're okay with it, I will be closing this issue now. If you have any further questions, please feel free to open a new one.

---

## Comment 5

> Username: anarthal  
> Created at: 2026-01-23 09:44:46 UTC  
> Updated at: 2026-01-23 09:45:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/343#issuecomment-3789357305  

For anyone reading this issue, since Boost 1.88, you can now use `any_connection::connection_id()` to retrieve an ID that can be used with `KILL QUERY`:  
  
```cpp  
co_await other_conn.async_execute(  
    with_params("KILL QUERY {}", blocked_conn.connection_id().value()),  
    result);  
```
