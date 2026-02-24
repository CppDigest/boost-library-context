# #103 - Can't read response of HELLO + PING + HELLO [Closed]

> Username: mzimbres  
> Created at: 2023-05-20 18:56:05 UTC  
> Updated at: 2023-06-04 14:50:13 UTC  
> Closed at: 2023-06-04 14:50:13 UTC  
> Url: https://github.com/boostorg/redis/issues/103  

There is an error when trying to read the response to the sequence of commands specified in the title in a non-generic response  
  
```cpp  
request req;  
req.push("HELLO", "3");  
req.push("PING", "Hello world");  
req.push("HELLO", "3");  
  
response<ignore_t, std::string, ignore_t> resp;  
co_await conn->async_exec(req, resp);  
```  
  
Results in  
  
```  
$ ./build/g++-11/cpp20_intro  
(Boost.Redis) Resolve results: 127.0.0.1:6379  
(Boost.Redis) Connected to endpoint: 127.0.0.1:6379  
(Boost.Redis) Bytes written: 161  
(main) Expects a resp3 simple type. [boost.redis:6]  
```
