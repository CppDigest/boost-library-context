# #108 - Bytes written... [Closed]

> Username: cyboy22  
> Created at: 2023-06-01 12:02:40 UTC  
> Updated at: 2023-06-02 06:35:58 UTC  
> Closed at: 2023-06-02 06:35:58 UTC  
> Url: https://github.com/boostorg/redis/issues/108  

Hi - Is there a way to disable the "Bytes Written..." message to std::cout (or std::err) as I cannot see anything obvious in the "config.hpp" header file.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-06-01 21:05:38 UTC  
> Url: https://github.com/boostorg/redis/issues/108#issuecomment-1572783197  

You can decrease the log level to, say, `crit`. For example  
  
```cpp  
conn->async_run(cfg, {boost::redis::logger::level::crit}, net::consign(net::detached, conn));  
```

---

## Comment 2

> Username: cyboy22  
> Created at: 2023-06-01 23:08:03 UTC  
> Url: https://github.com/boostorg/redis/issues/108#issuecomment-1572903914  

Thanks!
