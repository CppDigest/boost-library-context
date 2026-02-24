# #393 - Race condition when terminating servers in Windows CIs [Closed]

> Username: anarthal  
> Created at: 2025-01-01 18:55:17 UTC  
> Updated at: 2025-01-01 20:23:15 UTC  
> Closed at: 2025-01-01 20:23:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/393  

We use `os.kill` to terminate the example servers that we run in CIs. A permission denied error is sometimes raised, which happens if the process has exited before the `os.kill` call. This can happen when C++20 coroutines are not supported, for example.
