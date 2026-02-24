# #377 - Broken pipe error not showing up in the default log level [Closed]

> Username: mzimbres  
> Created at: 2026-01-04 17:42:50 UTC  
> Updated at: 2026-01-30 22:41:29 UTC  
> Closed at: 2026-01-30 22:41:29 UTC  
> Url: https://github.com/boostorg/redis/issues/377  

I noticed if I run my bencharks with [this](https://github.com/mzimbres/redis-cli-comp/blob/ef2f3adbf649b5f6a3711e3f68d331312e036dfa/apps/boost-redis/app_boost_redis.cpp#L26) parameter set to 5000 (large number of calls to `async_exec`) I get this error by default  
```  
$ ./tmp/bin/app_boost_redis  
(Boost.Redis) Trying to connect to Redis server at '/run/redis/redis-server.sock'  
(Boost.Redis) Connected to Redis server at '/run/redis/redis-server.sock'  
(Boost.Redis) Setup request execution: success  
Operation canceled [system:125]  
```  
  
which does not give a hint about what happened. After running the connection with `logger::level::debug` I get  
  
```  
(Boost.Redis) Writer task: 0 bytes written.  
(Boost.Redis) Writer task error: Broken pipe [system:32]  
(Boost.Redis) Reader task: cancelled (1)  
Operation canceled [system:125]  
```  
  
namely, there was an error in the writter task. The line is being logged with `debug` log level here  
  
https://github.com/boostorg/redis/blob/7750a6b126e0728dfcd4ade6d3becf12e1b04f4a/include/boost/redis/impl/writer_fsm.ipp#L92  
  
IMO an error like this should always be logged i.e. we should increase it to `level::emerg` so it always shows up in the log. For reference, this redis config parameter can be increase to avoid that error  
```conf  
client-output-buffer-limit pubsub 128mb 8mb 60  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2026-01-14 11:58:48 UTC  
> Url: https://github.com/boostorg/redis/issues/377#issuecomment-3749232525  

To add to this: when the push adapter reports an error, we should clearly state in logs that the error comes from the push adapter.
