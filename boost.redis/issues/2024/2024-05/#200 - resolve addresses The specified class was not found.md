# #200 - resolve addresses The specified class was not found [Closed]

> Username: liuaifu  
> Created at: 2024-05-21 12:27:55 UTC  
> Updated at: 2024-06-03 13:15:06 UTC  
> Closed at: 2024-06-03 13:15:06 UTC  
> Url: https://github.com/boostorg/redis/issues/200  

I'm using Boost1.85 + Redis7.0. I made a few changes to a example, and when the async_exec function is executed, it will output some error messages, which seem related to resolving domains. However, my connected IP address is 127.0.0.1, port 6379, which is the default value. I am very puzzled by this error message, please help me troubleshoot the problem. Thank you.  
  
```  
auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
conn->async_run(cfg, { boost::redis::logger::level::debug }, asio::consign(asio::detached, conn));  
  
request req;  
req.push("SET", "name", "zhangsan");  
req.push("GET", "name");  
  
generic_response resp;  
  
co_await conn->async_exec(req, resp, asio::deferred);  
conn->cancel();  
```  
  
```  
（Boost.Redis) run-all-op: resolve addresses The specified class was not found.  
......  
The I/O operation has been aborted because of either a thread exit or an application request.  
```

---

## Comment 1

> Username: criatura2  
> Created at: 2024-05-22 06:34:19 UTC  
> Url: https://github.com/boostorg/redis/issues/200#issuecomment-2123978549  

Can you show us how are you constructing `cfg`.

---

## Comment 2

> Username: liuaifu  
> Created at: 2024-05-22 12:27:28 UTC  
> Url: https://github.com/boostorg/redis/issues/200#issuecomment-2124669855  

```  
std::string g_redis_ip = "127.0.0.1";  
uint16_t g_redis_port = 6379;  
// ----------------------------------------  
config cfg;  
cfg.addr.host = g_redis_ip;  
cfg.addr.port = std::to_string(g_redis_port);  
```  
@criatura2

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-05-26 17:32:40 UTC  
> Url: https://github.com/boostorg/redis/issues/200#issuecomment-2132294438  

Did you manage to solve the problem? I don't see anything wrong with your code. Can you connect to this address using redis-cli? Sorry for being late to this conversation, I have had some busy days.

---

## Comment 4

> Username: liuaifu  
> Created at: 2024-06-03 13:13:55 UTC  
> Url: https://github.com/boostorg/redis/issues/200#issuecomment-2145174534  

This problem occurs on some computers, I did not solve it, using redis-cli can connect to the address, I changed to hiredis. Expect boost.redis to become more stable and I'll come back and use it
