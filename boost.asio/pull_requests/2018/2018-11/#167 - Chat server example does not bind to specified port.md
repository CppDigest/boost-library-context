# #167 Chat server example does not bind to specified port [Closed]

> Username: xtus  
> Created at: 2018-11-16 12:47:50 UTC  
> Updated at: 2020-12-30 02:01:44 UTC  
> Closed at: 2020-12-30 02:01:44 UTC  
> Url: https://github.com/boostorg/asio/pull/167  

Hi,  
  
I was trying the Coroutine TS chat server example and noticed that the server socket would bind to random ports, instead of the requested ones.  
  
As per following extract, the acceptor are instantiated in the coroutine via lambda, with the `port` captured by reference:  
  
https://github.com/boostorg/asio/blob/5ac54042c99d4f1595d4041b00b9b28752eda16e/example/cpp17/coroutines_ts/chat_server.cpp#L205-L211  
  
An `strace` report that the bind is on port `0`:  
```  
$ strace -e bind ./chat_server 8080 8081 8082  
bind(8, {sa_family=AF_INET, sin_port=htons(0), sin_addr=inet_addr("0.0.0.0")}, 16) = 0  
bind(9, {sa_family=AF_INET, sin_port=htons(0), sin_addr=inet_addr("0.0.0.0")}, 16) = 0  
bind(10, {sa_family=AF_INET, sin_port=htons(0), sin_addr=inet_addr("0.0.0.0")}, 16) = 0  
```  
  
As per patch, capturing the `port` by copy fix the issue.  
  
Test environment:  
* Linux  
* boost 1.68  
* clang 7.0.0  
* libc++ 7.0.0  
  
Regards,  
Marco

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 02:01:44 UTC  
> Url: https://github.com/boostorg/asio/pull/167#issuecomment-752300688  

Fixed in asio 1.13.0 / boost 1.70.

---
