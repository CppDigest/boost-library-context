# #197 - Per-operation cancellation in connection_pool [Closed]

> Username: anarthal  
> Created at: 2023-12-20 13:56:49 UTC  
> Updated at: 2024-09-21 19:10:32 UTC  
> Closed at: 2024-09-21 19:10:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/197  

Per-operation cancellation for `async_run` and `async_get_connection` doesn't do the right thing. It should:  
  
* `async_run`: same thing as `connection_pool::cancel`. This is partial cancellation.  
* `async_get_connection`: mark the operation as cancelled, then cancel the timer. Dispatch this via the pool executor. This is total cancellation.  
  
Document this.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 10:19:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/197#issuecomment-2280829766  

Now that `asio::cancel_after` and similar tokens exist, this is becoming more important. It may make sense to get rid of the overloads with a timeout and let `cancel_after` do the job.
