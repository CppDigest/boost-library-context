# #222 - connection_pool should be constructibe from an executor [Closed]

> Username: anarthal  
> Created at: 2024-02-16 15:41:39 UTC  
> Updated at: 2024-03-06 11:33:09 UTC  
> Closed at: 2024-03-06 11:33:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/222  

As per the current design, it is constructible from an execution context, or explicitly creating a `pool_executor_params` object.
