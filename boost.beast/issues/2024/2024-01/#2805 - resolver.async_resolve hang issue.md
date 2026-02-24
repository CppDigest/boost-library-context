# #2805 - resolver.async_resolve hang issue [Closed]

> Username: RavikumarTulugu  
> Created at: 2024-01-22 08:33:45 UTC  
> Updated at: 2024-01-22 12:55:44 UTC  
> Closed at: 2024-01-22 12:55:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2805  

I just copied the http_server_awaitable.cpp and altered a bit.   
I have removed the threads in the main and using only one default main thread. I have a requirement to call into another http server from the server requested handler, so I added the code from  http_client_ssl_awaitable.cpp in the server request handler.   
  
Now , i am seeing that the process hangs indefinitely in the resolver.resolve call. I am pasting the 2 lines here .   
wondering whether this is a bug or default behavior. please advise.   
  
```  
auto resolver = net::use_awaitable.as_default_on ( tcp::resolver ( co_await net::this_coro::executor ) );  
  beast::ssl_stream<tcp_stream> stream {   
    net::use_awaitable.as_default_on (   
      beast::tcp_stream ( co_await net::this_coro::executor ) ),    
      sslctx };  
  std::string host = "https://xyz.com", port = "443", target = "/token";  
  target += paramstring;  
  int version = 10;   
  _info << "resolving host : " << host << " port : " << port << std::endl;  
  auto const results = co_await resolver.async_resolve ( host, port );  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-22 08:59:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2805#issuecomment-1903531787  

It is likely throwing an exception with the error message `Host not found` because you included `https://` in the host name. you can catch and examine the exception message:  
  
```C++  
try  
{  
    auto const results = co_await resolver.async_resolve(host, port);  
}  
catch (const std::exception& e)  
{  
    std::cout << e.what() << std::endl;  
}  
```  
  
Using `www.xyz.com` as host name will resolve the issue.

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2024-01-22 12:55:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2805#issuecomment-1903951690  

Thanks alot, that was the issue.
