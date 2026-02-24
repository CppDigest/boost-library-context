# #229 - cpp20_resolve_with_sentinel.cpp example fails on response parsing [Closed]

> Username: maxoodf  
> Created at: 2025-01-17 13:06:40 UTC  
> Updated at: 2025-11-19 21:31:21 UTC  
> Closed at: 2025-11-19 21:31:21 UTC  
> Url: https://github.com/boostorg/redis/issues/229  

Hello,  
This example (cpp20_resolve_with_sentinel.cpp) incorrectly parses Sentinel server responses. Here are my logs:  
```  
(Boost.Redis) run-all-op: resolve addresses 192.168.88.81:26379  
(Boost.Redis) run-all-op: connected to endpoint 192.168.88.81:26379  
(Boost.Redis) writer-op: 161 bytes written.  
(Boost.Redis) reader-op: 229 bytes read.  
(Boost.Redis) reader-op: parse error. Exiting ...  
(Boost.Redis) hello-op: Success  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Got RESP3 simple-error. (reader), Success (writer)  
(Boost.Redis) ping_op: error/cancelled (2).  
(Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) runner-op: Got RESP3 simple-error. (async_run_all), Operation canceled (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Got RESP3 simple-error.  
```  
  
However, if I remove this line  `req.push("QUIT");`, everything works as expected:  
```  
(Boost.Redis) run-all-op: resolve addresses 192.168.88.81:26379  
(Boost.Redis) run-all-op: connected to endpoint 192.168.88.81:26379  
(Boost.Redis) writer-op: 147 bytes written.  
(Boost.Redis) reader-op: 172 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) ping_op: error/cancelled (2).  
(Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Operation canceled (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-02-02 21:34:00 UTC  
> Url: https://github.com/boostorg/redis/issues/229#issuecomment-2629564276  

Hi and thanks for reporting. I will try to fix this soon.
