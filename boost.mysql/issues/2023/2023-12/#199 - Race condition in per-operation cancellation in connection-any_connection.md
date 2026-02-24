# #199 - Race condition in per-operation cancellation in connection/any_connection [Closed]

> Username: anarthal  
> Created at: 2023-12-20 13:59:51 UTC  
> Updated at: 2025-02-11 18:15:52 UTC  
> Closed at: 2025-02-11 18:15:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/199  

This is a rare case of a race condition that can happen during the execution of any connection function. If the cancellation signal is emitted while the current async op is complete, but the handler hasn't been called yet (is queued), the cancellation has no effect and the operation continues.  
  
This can be fixed by checking the composed op's cancellation state in `async_run_algo_impl`.
