# #288 - Improve error handling when server response can't be accommodated in the response object [Open]

> Username: mzimbres  
> Created at: 2025-07-25 19:11:58 UTC  
> Updated at: 2025-07-25 19:11:58 UTC  
> Url: https://github.com/boostorg/redis/issues/288  

For example  
  
```cpp  
request req;  
req.push("ping", "abc");  
req.push("ping", "def");  
  
response<std::string> res;  
  
co_await conn.async_exec(req, res);  
```  
Allowing internal adapter code is wrong, the library should communicate an error with which the async operation will complete.  
  
Note: This touches only responses with an static size i.e. `response<T1, T2, ...>` and not the `generic_response`.
