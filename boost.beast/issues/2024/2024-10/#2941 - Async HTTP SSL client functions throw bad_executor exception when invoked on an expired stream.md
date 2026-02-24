# #2941 - Async HTTP SSL client functions throw bad_executor exception when invoked on an expired stream [Closed]

> Username: petrbel  
> Created at: 2024-10-17 13:17:23 UTC  
> Updated at: 2024-10-29 08:43:19 UTC  
> Closed at: 2024-10-29 08:43:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2941  

### Version of Beast  
  
1.86.0-3  
  
### Description  
Between boost 1.83 and 1.86, there is a change of behavior of async client functions.  
During long http requests in which the connection has exceeded the set stream expiration, the async functions throw instances of `bad_executor` exception.  
This is an unexpected behavior.  
  
In boost 1.86: the async functions throw `bad_executor` exception if the SSL stream is expired  
In boost 1.83: no exception is thrown, instead, `ec` contains a valid information `The socket was closed due to a timeout`  
  
### Steps necessary to reproduce the problem  
One way of simulating a long operation is sleeping before some async operation, so that the stream expiration time is exceeded.  
  
My steps:  
1. Download the [http client async ssl example](https://github.com/boostorg/beast/blob/develop/example/http/client/async-ssl/http_client_async_ssl.cpp)  
1. Inside `on_handshake` function: add `std::this_thread::sleep_for(std::chrono::seconds(X));` just before calling `http::async_write`. Set `X` to be greater than the value specified in `expires_after`  
1. Compile and run the example (boost 1.86). The output is an unexpected exception `boost::wrapexcept<boost::asio::execution::bad_executor>`  
1. Compile and run the example (boost 1.83). The output is `read: The socket was closed due to a timeout`  
  
> A small compiling program is the best. If your code is  
> public, you can provide a link to the repository.  
  
I provide a link to github gist: https://gist.github.com/petrbel/1814f53e9aab4ba713c51047f04e8749  
  
There are a some minor changes from the original example. Please see [the diff](https://gist.github.com/petrbel/1814f53e9aab4ba713c51047f04e8749/revisions#diff-54f94c60462c24fdb13ac72bade99096dabd3b2947b7610f54c6815ea8bfa822):  
1. Don't load root certificates  
1. Set stream expiration to only 3s instead of 30s (just to run everything faster)  
1. Use `ssl::verify_none` verification policy  
1. Sleep before some async function (in this case in `on_handshake` function just before calling `http::async_write`)  
  
### All relevant compiler information  
I am able to compile. I use this command for compilation:  
  
```bash  
g++ -o http-client-async-ssl  -lssl  -lcrypto -lboost_system -lboost_thread -lboost_filesystem http_client_async_ssl.cpp  
```  
  
This is the compiler I use:  
```  
$ g++ --version  
g++ (GCC) 14.2.1 20240910  
Copyright (C) 2024 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
Execution of the program:  
```bash  
./http-client-async-ssl www.boost.org 443 /  
```  
  
### Conclusion  
Please let me know if I can provide any further assistance/clarification/example.  
  
FYI @FloopCZ @honzov

---

## Comment 1

> Username: ashtum  
> Created at: 2024-10-17 13:52:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2941#issuecomment-2419615035  

Thank you for the report and the reproducible example.  
I can confirm the issue and it is already fixed in #2926 and will be included in the next Boost release.
