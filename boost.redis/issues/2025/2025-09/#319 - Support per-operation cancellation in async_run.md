# #319 - Support per-operation cancellation in async_run [Closed]

> Username: anarthal  
> Created at: 2025-09-27 16:46:09 UTC  
> Updated at: 2025-10-03 16:48:32 UTC  
> Closed at: 2025-10-03 16:48:32 UTC  
> Url: https://github.com/boostorg/redis/issues/319  

This would enable fixing #318 and would make us support `conn.async_run(cfg, asio::cancel_after(10s))` for free.
