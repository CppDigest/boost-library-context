# #83 - Fix reconnect loop in the subscriber example [Closed]

> Username: mzimbres  
> Created at: 2023-03-11 11:48:38 UTC  
> Updated at: 2023-03-16 17:05:42 UTC  
> Closed at: 2023-03-16 17:05:42 UTC  
> Url: https://github.com/boostorg/redis/issues/83  

The `connect` function used in the subscriber example throws on resolve and connect timeouts in which case the app will exit. This behaviour should be changed so that those errors are caught and the body of the loop must continue to the timer so it can try to resolve/connect again.  
  
This is such a fundamental feature for Aedis that it might be a better idea to just provide a high-level function that performs these tasks. It would look like  
  
```cpp  
auto async_run(conn, parameters, token);  
```  
  
This function would  
  
1. Resolve  
2. Connect  
3. Call member `async_run`  
4. Spawn an `async_check_health`  
5. Spawn an op that sends `HELLO` and authenticates.  
6. Loop on the above.  
  
Similar to https://github.com/boostorg/redis/blob/52e62ba78cac136301ee92361fb0afc91853a1b5/examples/cpp20_subscriber.cpp#L48
