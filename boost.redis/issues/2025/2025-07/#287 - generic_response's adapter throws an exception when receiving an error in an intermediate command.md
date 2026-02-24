# #287 - generic_response's adapter throws an exception when receiving an error in an intermediate command [Closed]

> Username: anarthal  
> Created at: 2025-07-25 14:15:31 UTC  
> Updated at: 2025-09-01 18:18:36 UTC  
> Closed at: 2025-09-01 18:18:36 UTC  
> Url: https://github.com/boostorg/redis/issues/287  

The following throws when it shouldn't:  
  
```cpp  
   config cfg;  
  
   request req;  
   req.push("PING", "hello");  
   req.push("set", "mykey");  
   req.push("get", "mykey");  
  
   boost::redis::generic_response resp;  
  
   asio::io_context ioc;  
   connection conn{ioc};  
  
   conn.async_run(cfg, asio::detached);  
  
   conn.async_exec(req, resp, [&](auto, auto) {  
      std::cout << "on async_exec" << std::endl;  
      conn.cancel();  
   });  
  
   ioc.run();  
```  
  
The server will respond with, at least, with nodes like the following: [success, error, success].  
  
This causes the adapter to behave incorrectly in [these lines](https://github.com/boostorg/redis/blob/20ab2c7e70ad9c9bd696acc69644a85c645c77bc/include/boost/redis/adapter/detail/adapters.hpp#L142-L171). The error node will emplace an error response in the result, and the next success node will invoke `result::value()`, throwing an exception.  
  
I don't think this is super urgent but should be kept track of. If I have the time to refactor adapters at some point, I'll fix it.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-07-25 14:38:30 UTC  
> Url: https://github.com/boostorg/redis/issues/287#issuecomment-3118168383  

Might actually not require any refactor at all, just checking if there has been a previous error.

---

## Comment 2

> Username: mzimbres  
> Created at: 2025-07-25 18:49:34 UTC  
> Url: https://github.com/boostorg/redis/issues/287#issuecomment-3119975660  

Humm, I think we should fix this ASAP because it might frustrate users to spend an hour debugging their code just to find out the `SET` command syntax is wrong. AFAICS we need a error handling like [this](https://github.com/boostorg/redis/blob/20ab2c7e70ad9c9bd696acc69644a85c645c77bc/include/boost/redis/adapter/detail/adapters.hpp#L476).  
  
Also, I think exception should be avoided in async code in general, and there are some more errors conditions that the adapters can catch instead of allowing an exception being thrown.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-07-25 19:50:10 UTC  
> Url: https://github.com/boostorg/redis/issues/287#issuecomment-3120143566  

OK, I'll submit a PR tomorrow then.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-07-27 08:45:18 UTC  
> Url: https://github.com/boostorg/redis/issues/287#issuecomment-3124238153  

Note that, in the same direction, I think that classes like `generic_response` should be cleared in the parse initialization event, discarding any previous values (or errors) that the user might have supplied.

---

## Comment 5

> Username: mzimbres  
> Created at: 2025-07-27 14:32:27 UTC  
> Url: https://github.com/boostorg/redis/issues/287#issuecomment-3124456471  

> Note that, in the same direction, I think that classes like generic_response should be cleared in the parse initialization event, discarding any previous values (or errors) that the user might have supplied.  
  
That might make sense in some cases but I think it would make adapters opinionated in a negative way, for example, asio and beast also do not clear buffers passed by the user.  
  
For example, a single socket read might pull hundreds of pushes into the read buffer. Delivering them one by one with async channel operations is comparatively very ineficient, so what Boost.Redis does is to try to deliver them synchronously with `try_send` until the channel is full. That means when `async_receive` resumes the `generic_response` might contain hundreds of individual pushes which are that consumed with the `consume_one` function.
