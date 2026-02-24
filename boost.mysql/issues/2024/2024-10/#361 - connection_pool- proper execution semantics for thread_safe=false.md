# #361 - connection_pool: proper execution semantics for thread_safe=false [Closed]

> Username: anarthal  
> Created at: 2024-10-03 19:46:14 UTC  
> Updated at: 2024-10-10 13:52:18 UTC  
> Closed at: 2024-10-10 13:52:18 UTC  
> Url: https://github.com/boostorg/mysql/issues/361  

While `async_get_connection` respects the usual Asio semantics for pools with `thread_safe=false`, connection tasks, which are technically children agents of `async_run`, do not. The proper semantics should be:  
  
- For thread_safe=false:  
    - `async_run` intermediate handlers (e.g. notification timers): usual Asio semantics (as is today)  
    - Connection task final handlers: since these affect pool state, usual Asio semantics (i.e. if `async_run` token has a bound executor, use this; otherwise, use the pool's executor). Currently, we always use the pool's executor. EDIT: these should always use the pool's executor, as it is today.  
    - Connection task intermediate handlers (e.g. the ones run by `connection::async_connect`): if `async_run` token has a bound executor, use this. Otherwise, use the connection's executor. EDIT: these should always use the pool's executor, as it is today.  
- For thread_safe=true:  
    - `async_run` intermediate handlers (e.g. notification timers): use the strand (as is today)  
    - Connection task final handlers: use the strand (as is today)  
    - Connection task intermediate handlers: there's no need to use the strand here, as these don't access pool state. EDIT: these can cause race conditions on the connections, too, so should use the pool's executor.  
  
These should be documented.  
  
EDIT: following the discussion in Slack, and given that using `async_run` token's for connection tasks is difficult, the current approach is good, but should be documented.
