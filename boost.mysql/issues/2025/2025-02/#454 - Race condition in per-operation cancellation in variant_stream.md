# #454 - Race condition in per-operation cancellation in variant_stream [Closed]

> Username: r33s3n6  
> Created at: 2025-02-25 06:24:38 UTC  
> Updated at: 2025-02-26 15:49:52 UTC  
> Closed at: 2025-02-26 15:49:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/454  

#### **Description**    
I've encountered issues with ineffective cancellation when executing long-running SQL queries. While not consistently reproducible, this issue appears to occur under certain conditions with any SQL query.    
  
#### **Steps to Reproduce**    
The issue occurs when executing the following code, with all tasks running on a single `io_context` on a single thread:    
  
```cpp  
auto [ec] = co_await conn.async_execute(  
    sql, mysql_result, diag, asio::cancel_after(timeout, asio::as_tuple(asio::deferred)));  
```  
    
In my testing, if the SQL query is slow and the result set is large, the cancellation timer is more likely to be triggered in the following scenario:    
1. An `async_read_some()` operation completes.    
2. The completion token for `async_read_some()` is queued but has not yet been executed.    
3. The timer handler executes but fails to cancel anything, as no outstanding asynchronous operation is active at that moment.    
4. The `async_read_some()` completion token is then executed, calling `run_algo_op`, which does not properly account for this situation.    
  
#### **Possible Cause and Fix**    
It seems necessary to check `self.cancelled() != asio::cancellation_type::none` within each loop iteration to ensure proper cancellation handling. This issue affects not only `run_algo_op`, but also all state machines initiated by `asio::async_compose` (e.g., `variant_stream::connect_op`).    
  
Any insights or potential fixes would be greatly appreciated. Thanks!

---

## Comment 1

> Username: r33s3n6  
> Created at: 2025-02-25 07:32:05 UTC  
> Updated at: 2025-02-25 07:32:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/454#issuecomment-2680940764  

Sorry for overlooking #199 earlier; however, could it be that `variant_stream::connect_op` also needs a fix?

---

## Comment 2

> Username: anarthal  
> Created at: 2025-02-25 09:53:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/454#issuecomment-2681384443  

Your diagnostics are absolutely correct. It looks like I overlooked the `variant_stream` part, thanks for noticing.
