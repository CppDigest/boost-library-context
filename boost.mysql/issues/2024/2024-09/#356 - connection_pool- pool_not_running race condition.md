# #356 - connection_pool: pool_not_running race condition [Closed]

> Username: anarthal  
> Created at: 2024-09-27 17:44:57 UTC  
> Updated at: 2024-09-30 19:38:31 UTC  
> Closed at: 2024-09-30 19:38:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/356  

The current scheme where `async_get_connection` fails immediately with `client_errc::pool_not_running` if the pool is not running is good for diagnosis but can cause problems at application startup, specially when running integration tests.  
  
We're experiencing this in the thread_safety tests, which randomly fail because there is no way to "wait for a pool to start running". This is likely going to cause trouble in any other integration testing setup.  
  
I think it'd be best to make `async_get_connection` wait even if the pool is not running, and provide the specific error code if the operation is cancelled before the pool starts running.
