# #49 - HGETALL fails with if not sent together with HELLO [Closed]

> Username: jsaf0  
> Created at: 2023-01-20 13:22:16 UTC  
> Updated at: 2023-01-20 17:31:55 UTC  
> Closed at: 2023-01-20 17:31:55 UTC  
> Url: https://github.com/boostorg/redis/issues/49  

I'm trying to read a hash using `HGETALL` into a `std::map<std::string, std::string>`. It fails unless the requests starts with a `req.push("HELLO", 3)`.  
  
Doesn't work (fails with `Expects resp3 map. [aedis:8]`):  
```  
aedis::resp3::request req;  
req.push("HGETALL", key);  
std::tuple<std::map<std::string, std::string>> resp;  
co_await conn_->async_exec(req, aedis::adapt(resp), use_awaitable);  
```  
However, it works when sending HELLO first (like the examples).  
```  
aedis::resp3::request req;  
req.push("HELLO", 3);  
req.push("HGETALL", key);  
std::tuple<aedis::ignore, std::map<std::string, std::string>> resp;  
co_await conn_->async_exec(req, aedis::adapt(resp), use_awaitable);  
```  
The documentation is not clear about when it's necessary to send a HELLO first.

---

## Comment 1

> Username: jsaf0  
> Created at: 2023-01-20 17:31:55 UTC  
> Url: https://github.com/boostorg/redis/issues/49#issuecomment-1398721347  

I can see that automatic sending of HELLO was removed in v1.3.0-1. Will close issue as not an issue.
