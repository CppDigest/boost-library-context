# #350 - connection_pool: remove pool_executor_params [Closed]

> Username: anarthal  
> Created at: 2024-09-21 19:13:29 UTC  
> Updated at: 2024-10-03 04:28:45 UTC  
> Closed at: 2024-10-03 04:28:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/350  

This type made sense with the old thread_safe functions. We can probably merge the connection_executor parameter into `pool_params`.
