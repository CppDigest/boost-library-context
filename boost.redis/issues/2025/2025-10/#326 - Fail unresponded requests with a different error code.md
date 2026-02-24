# #326 - Fail unresponded requests with a different error code [Open]

> Username: anarthal  
> Created at: 2025-10-06 15:30:04 UTC  
> Updated at: 2025-10-06 15:30:04 UTC  
> Url: https://github.com/boostorg/redis/issues/326  

We offer `config::cancel_if_unresponded`, which makes a request fail if it was sent to the server but never responded because the connection was severed. There is no way to know whether these requests were processed by the server or not. At the moment, `async_exec` completes with `asio::operation_aborted` for these requests. This makes it impossible for the user to know when this situation happened. We should create a new error code for this situation. See #324 for the discussion.
