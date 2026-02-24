# #2724 - Example coroutine server triggers ubsan [Closed]

> Username: uvxwx  
> Created at: 2023-08-18 00:29:28 UTC  
> Updated at: 2024-01-18 12:12:01 UTC  
> Closed at: 2024-01-18 12:12:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2724  

[This example C++20 coroutine server](https://www.boost.org/doc/libs/1_83_0/libs/beast/example/http/server/awaitable/http_server_awaitable.cpp) triggers ubsan when being tested with [wrk](https://github.com/wg/wrk).  
  
### Version of Beast  
```  
345, boost 1.81  
```  
### Steps necessary to reproduce the problem  
```  
clang++-16 -std=c++20 -ggdb3 -fsanitize=undefined http_server_awaitable.cpp -o beast  
./beast 127.0.0.1 8080 . 4  
dd if=/dev/zero of=test bs=10M count=1  
wrk -c 1000 -d 60 -t 4 http://127.0.0.1:8080/test  
```  
  
### All relevant compiler information  
Debian 13 testing  
```  
Debian clang version 16.0.6 (10)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
The same behavior can be reproduced with gcc 13.2.0, clang 15.0.7.  
### ubsan output  
[ubsan.txt](https://github.com/boostorg/beast/files/12374021/ubsan.txt)  
### wrk output  
Running 1m test @ http://127.0.0.1:8080/test  
  4 threads and 1000 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency     0.00us    0.00us   0.00us    -nan%  
    Req/Sec    28.26     92.06   610.00     96.55%  
  213 requests in 1.00m, 4.48GB read  
  Socket errors: connect 0, read 743, write 1726390, timeout 213  
  Non-2xx or 3xx responses: 1  
Requests/sec:      3.54  
Transfer/sec:     76.38MB

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-01 10:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2724#issuecomment-1873258454  

@uvxwx I couldn't reproduce the mentioned sanitizer error. I tested it with Clang 16.0.4, Clang 15.0.7, and GCC 12.3.0.  
  
There is a good chance that this is a bug in the `libstdc++` on your platform. Have you tried using `libc++` or updating the existing `libstdc++`?
