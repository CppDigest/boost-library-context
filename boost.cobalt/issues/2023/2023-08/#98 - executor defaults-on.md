# #98 - executor defaults-on [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-08-31 05:39:22 UTC  
> Updated at: 2024-05-30 15:00:44 UTC  
> Closed at: 2024-05-30 15:00:43 UTC  
> Url: https://github.com/boostorg/cobalt/issues/98  

The result of  
  
```cpp  
co_await this_coro::executor;  
```  
  
should give us an executor with a bound default token, so that this is valid:  
  
```cpp  
auto exec = co_await this_coro::executor;;  
co_await asio::post(exec);  
````

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-05-30 15:00:43 UTC  
> Url: https://github.com/boostorg/cobalt/issues/98#issuecomment-2139826074  

That doesn't really help to construct sockets etc.
