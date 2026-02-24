# #284 - async_exec never timeouts [Closed]

> Username: Chrys4lisfag  
> Created at: 2025-07-13 11:13:32 UTC  
> Updated at: 2025-10-11 20:36:51 UTC  
> Closed at: 2025-10-11 20:36:51 UTC  
> Url: https://github.com/boostorg/redis/issues/284  

Hello! Recently, I've faced an issue in my server with running out file handles. I've checked the process handles and located the problematic place.  
  
I assume async_exec op never finishes due to some connection issues and prevents ofstream from closing the file.  
My redis config is mostly default, so it uses default timeouts.  
  
I can fix this issue by providing boost::asio::cancel_after(30s) completion token to async_exec but is this expected behavior?  
  
Using boost-1.88.0  
  
```cpp  
boost::asio::awaitable<bool> some_redis_op()  
{  
    try  
    {  
        boost::redis::request request;  
		boost::redis::response<int> response;  
  
        co_await ctx::redis->async_exec( request, response,  
          boost::asio::bind_executor( ctx::redis->get_executor(), boost::asio::deferred ) );  
  
        co_return response.get() == 1; // Assuming the operation returns an integer      
    }  
    catch (const std::exception& e)  
    {  
        co_return false;  
    }  
}  
  
  
boost::asio::awaitable<void> come_coro()  
{  
    try  
    {  
        for (const auto& item : some_collection)  
        {  
            std::ofstream ofs("save_data.txt", std::ios::app); // THIS FILE NEVER CLOSES AND   
            // write here  
            const auto check_some_data = co_await some_redis_op(); // I assume this operation hungs infinitely  
  
            // some logic  
        }  
  
        co_return;   
    }  
    catch (const std::exception& e)  
    {  
            co_return;   
    }  
}  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2025-07-13 16:31:07 UTC  
> Url: https://github.com/boostorg/redis/issues/284#issuecomment-3067153451  

I think this is expected behavior (please @mzimbres confirm). The correct way to work this around is using `cancel_after` as you suggested. I don't believe that adding another timeout is a good idea, since it'd be replicating what `cancel_after` does.  
  
Note that there are still some implementation gaps regarding cancellation, which I want to address for the next release. Currently, cancelling requests that have been sent to the server but haven't been answered yet will cause the connection to be cancelled, as if calling `connection::cancel()`. I don't think this is the most intuitive behavior, but it is what it is. You might consider calling `async_run` in a loop as a workaround. On the other hand, cancelling requests that haven't been written to the server yet works fine (this is the case if you call `async_exec` and the server is offline).

---

## Comment 2

> Username: Chrys4lisfag  
> Created at: 2025-07-13 18:35:12 UTC  
> Updated at: 2025-07-13 18:35:53 UTC  
> Url: https://github.com/boostorg/redis/issues/284#issuecomment-3067232907  

> I think this is expected behavior (please [@mzimbres](https://github.com/mzimbres) confirm). The correct way to work this around is using `cancel_after` as you suggested. I don't believe that adding another timeout is a good idea, since it'd be replicating what `cancel_after` does.  
>   
> Note that there are still some implementation gaps regarding cancellation, which I want to address for the next release. Currently, cancelling requests that have been sent to the server but haven't been answered yet will cause the connection to be cancelled, as if calling `connection::cancel()`. I don't think this is the most intuitive behavior, but it is what it is. You might consider calling `async_run` in a loop as a workaround. On the other hand, cancelling requests that haven't been written to the server yet works fine (this is the case if you call `async_exec` and the server is offline).  
  
Your answers are the best, as always! I checked my logs, and you were spot on. The async_run call was failing with badly readable error "boost.redis:18" due to me passing boost::system::system_error in completion token lambda :)  
  
Regarding the request cancelation on connection lost I found this one in the request config which differs from your answer and seems like it has some internal timeout system. (might be just system socket internal timeout so it is indeed better you use cancel_after token). Anyway, it would be good to add cancel_after in the readme example as it has been not so long as I personally found out about this token.  
  
/** @brief If `true`, calls to @ref basic_connection::async_exec will  
* complete with error if the connection is lost while the  
* request hasn't been sent yet.  
*/  
bool cancel_on_connection_lost = true;  
  
  
/** @brief If `false`, @ref basic_connection::async_exec will not  
* automatically cancel this request if the connection is lost.  
* Affects only requests that have been written to the socket  
* but have not been responded when  
* @ref boost::redis::connection::async_run completes.  
*/  
bool cancel_if_unresponded = true;  
  
  
Thanks again! The issue can be closed if it is not needed for the maintainers/

---

## Comment 3

> Username: anarthal  
> Created at: 2025-07-13 19:57:48 UTC  
> Url: https://github.com/boostorg/redis/issues/284#issuecomment-3067270904  

IIRC (Marcelo can confirm this) these settings apply when there is a queued request and the connection is lost. Roughly, if your request is queued and connection is lost:  
  
* If the request has already been sent to the server, it will complete with an error only if cancel_if_unresponded is true.  
* If the request has NOT been sent to the server, it will complete with an error only if  cancel_on_connection_lost is true.  
  
Now, if you enqueue a request and the connection is not established, then if cancel_if_not_connected is true, the request will be cancelled even before trying to send it.  
  
There is a problem with all these, and is that currently the connection doesn't use the correct definition of what an "established connection" is. It uses a TCP primitive, which is not correct. IIRC #236 applies. I've got plans to fix this, but it'll take some time.

---

## Comment 4

> Username: mzimbres  
> Created at: 2025-10-11 19:30:51 UTC  
> Url: https://github.com/boostorg/redis/issues/284#issuecomment-3393612031  

Hi @anarthal I think with the cancel_after support that you got merge we can close this.
