# #69 - async::with should be able to return a value [Closed]

> Username: anarthal  
> Created at: 2023-08-12 15:00:00 UTC  
> Updated at: 2023-09-05 03:10:12 UTC  
> Closed at: 2023-09-05 03:10:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/69  

This code is a little bit verbose:  
  
```  
    error_code ec;  
  
    // Launch a Redis client in the background. Cancel it on exit  
    co_await boost::async::with(redis_runner(st), [&](redis_runner&) -> promise<void> {  
        // Run the listening loop  
        ec = co_await run_listener(listening_endpoint, st);  
    });  
  
    co_return ec;  
```  
  
Would be great like:  
  
```  
    // Launch a Redis client in the background. Cancel it on exit  
    co_return co_await boost::async::with(redis_runner(st), [&](redis_runner&) -> promise<error_code> {  
        // Run the listening loop  
        co_return co_await run_listener(listening_endpoint, st);  
    });  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-14 09:27:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/69#issuecomment-1676991456  

That should be   
  
```cpp  
// Launch a Redis client in the background. Cancel it on exit  
    co_return co_await boost::async::with(redis_runner(st), [&](redis_runner&) -> promise<error_code> {  
        // Run the listening loop  
        return run_listener(listening_endpoint, st);  
    });  
```  
  
I reckon.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-08-14 09:38:04 UTC  
> Url: https://github.com/boostorg/cobalt/issues/69#issuecomment-1677005910  

Seems equivalent.
