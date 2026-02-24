# #29 - Dead lock if cancellation is requested while there is a PING request on the fly [Closed]

> Username: nejati-deriv  
> Created at: 2022-09-22 14:42:54 UTC  
> Updated at: 2022-10-26 07:20:25 UTC  
> Closed at: 2022-10-26 07:20:25 UTC  
> Url: https://github.com/boostorg/redis/issues/29  

I'm not sure but problem seems to be [here](https://github.com/mzimbres/aedis/blob/master/include/aedis/detail/connection_ops.hpp#L298).  
If I am not mistaken you used `close_on_run_completion` flag exactly to prevent this situation but I don't know why it leads to dead lock any way.  
  
You can reproduce it with a loop like:  
```C++  
for (;;)  
{  
    auto timer = asio::steady_timer{ executor, std::chrono::seconds{ 1 } };  
    co_await (connection.async_run(endpoint, asio::use_awaitable) || timer.async_wait(asio::use_awaitable));  
}  
```  
if you change timer to something shorter than ping time like 800ms it will not lead to dead lock.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-09-22 15:44:37 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1255216370  

Can you please try what I said here https://github.com/mzimbres/aedis/issues/28#issuecomment-1255214596 and let me know if the problem persists.

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-09-24 09:43:13 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1256925881  

I can't reproduce this behaviour. I have used this code https://github.com/mzimbres/aedis/issues/28#issuecomment-1256921527 with a timeout of `1s` and `2s` and it keeps printing retrying. A deadlock may occur for example when Redis for any reason sends unsolicited messages and you don't consume them, can you please add a coroutine to consume them, for example  
```  
net::awaitable<void> push_receiver(std::shared_ptr<connection> db)  
{  
   for (std::vector<node<std::string>> resp;;) {  
      co_await db->async_receive_push(adapt(resp));  
      print_push(resp);  
      resp.clear();  
   }  
}  
```

---

## Comment 3

> Username: nejati-deriv  
> Created at: 2022-09-25 16:39:11 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1257230969  

Adding more connections increases the chance:  
  
```C++  
  
#include <boost/asio.hpp>  
#include <boost/asio/experimental/awaitable_operators.hpp>  
  
#include <aedis.hpp>  
#include <chrono>  
#include <iostream>  
  
namespace net = boost::asio;  
using namespace net::experimental::awaitable_operators;  
using aedis::endpoint;  
using connection = aedis::connection<>;  
  
net::awaitable< void >  
reconnect()  
{  
    auto db1   = connection{ co_await net::this_coro::executor };  
    auto db2   = connection{ co_await net::this_coro::executor };  
    auto db3   = connection{ co_await net::this_coro::executor };  
    auto db4   = connection{ co_await net::this_coro::executor };  
    auto timer = net::steady_timer{ co_await net::this_coro::executor };  
    for (int i = 0;; i++)  
    {  
        timer.expires_after(std::chrono::milliseconds{ 1005 }); // Note extra 5ms  
        endpoint ep{ "127.0.0.1", "6379" };  
        co_await (db1.async_run(ep, net::use_awaitable) || db2.async_run(ep, net::use_awaitable) ||  
                  db3.async_run(ep, net::use_awaitable) || db4.async_run(ep, net::use_awaitable) ||  
                  timer.async_wait(net::use_awaitable));  
        std::cout << i << std::endl;  
    }  
}  
  
int  
main()  
{  
    net::io_context ioc;  
    net::co_spawn(ioc, reconnect(), net::detached);  
    ioc.run();  
}  
  
```

---

## Comment 4

> Username: mzimbres  
> Created at: 2022-09-25 16:46:56 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1257232229  

Does this trigger a sanitizer check on your machine?

---

## Comment 5

> Username: nejati-deriv  
> Created at: 2022-09-25 17:31:23 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1257240784  

Yes `alloc-dealloc-mismatch` and `use-of-uninitialized-value` with a gigantic error messages.  
[alloc-dealloc-mismatch.txt](https://github.com/mzimbres/aedis/files/9641383/alloc-dealloc-mismatch.txt)  
[use-of-uninitialized-value.txt](https://github.com/mzimbres/aedis/files/9641385/use-of-uninitialized-value.txt)

---

## Comment 6

> Username: mzimbres  
> Created at: 2022-10-23 20:35:04 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1288195663  

I have made many improvements in cancellation in master. I can't reproduce this problem anymore, so it might have been fixed.

---

## Comment 7

> Username: nejati-deriv  
> Created at: 2022-10-24 09:24:37 UTC  
> Updated at: 2022-10-24 09:25:10 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1288720320  

Thanks for you work,  
I can still reproduce it with the following code: (delay 1 ms)  
```C++  
#include <boost/asio.hpp>  
#include <boost/asio/experimental/awaitable_operators.hpp>  
  
#include <aedis.hpp>  
#include <chrono>  
#include <iostream>  
  
namespace net = boost::asio;  
using namespace net::experimental::awaitable_operators;  
using aedis::endpoint;  
using connection = aedis::connection<>;  
  
net::awaitable< void >  
reconnect()  
{  
    auto db1   = connection{ co_await net::this_coro::executor };  
    auto db2   = connection{ co_await net::this_coro::executor };  
    auto db3   = connection{ co_await net::this_coro::executor };  
    auto db4   = connection{ co_await net::this_coro::executor };  
    auto timer = net::steady_timer{ co_await net::this_coro::executor };  
    for (int i = 0;; i++)  
    {  
        timer.expires_after(std::chrono::milliseconds{ 1 });   // Note 1 ms  
        endpoint ep{ "172.17.0.1", "6379" };  
        co_await (db1.async_run(ep, {}, net::use_awaitable) || db2.async_run(ep, {}, net::use_awaitable) ||  
                  db3.async_run(ep, {}, net::use_awaitable) || db4.async_run(ep, {}, net::use_awaitable) ||  
                  timer.async_wait(net::use_awaitable));  
        std::cout << i << std::endl;  
    }  
}  
  
int  
main()  
{  
    net::io_context ioc;  
    net::co_spawn(ioc, reconnect(), net::detached);  
    ioc.run();  
}  
```

---

## Comment 8

> Username: mzimbres  
> Created at: 2022-10-25 19:02:00 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1291010633  

Thanks for insisting :) I learned many sutilities in the Asio cancellation mechanism. This commit should fix the problem https://github.com/mzimbres/aedis/commit/bac27c1770a14c47cd802eb556983d14e93df013. Let me know if it works for you.

---

## Comment 9

> Username: nejati-deriv  
> Created at: 2022-10-26 07:01:31 UTC  
> Url: https://github.com/boostorg/redis/issues/29#issuecomment-1291587865  

Seems that fixed the problem, thanks :partying_face:
