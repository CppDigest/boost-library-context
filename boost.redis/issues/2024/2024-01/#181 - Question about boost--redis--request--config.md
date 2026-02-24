# #181 - Question about boost::redis::request::config [Closed]

> Username: redboltz  
> Created at: 2024-01-25 03:28:18 UTC  
> Updated at: 2024-02-06 20:14:37 UTC  
> Closed at: 2024-02-06 20:14:37 UTC  
> Url: https://github.com/boostorg/redis/issues/181  

[boost::redis::request::config](]https://www.boost.org/doc/libs/1_84_0/libs/redis/doc/html/classboost_1_1redis_1_1request.html#structboost_1_1redis_1_1request_1_1config) has four member variables.  
I want to know the behavior of cancel or not cancel.  
  
I assume that redis server is running, and `async_run()` is called with reconnect_wait_interval 10 seconds.  
  
## Case1: async_exec during disconnected  
1. restart redis server by systemctl. (server restarts immediately)  
2. User calls asnyc_exec  
3. 10 seconds later from step1 Boost.Redis reconnect to the server  
4. CompletionHandler of step2 is invoked **if cancel_if_not_connected is false**, otherwise the request step2 is cancelled.  
  
I tested it by my enviromnent, and works as above. It is expected behavior for me.  
  
## Case2: disconnect after async_exec called but not completion handler is invoked.  
  
1. User calls asnyc_exec  
2. restart redis server by systemctl. (server restarts immediately)  
3. 10 seconds later from step2 Boost.Redis reconnect to the server  
4. ???  
  
I am looking for the way that Boost.Redis resends step1 request to the server and receives the response and then, invoke the completion handler of the step1. So the user doesn't need to care about disconnection/resending.  
  
I guess that `boost::redis::request::config` member `cancel_if_unresponded` and `cancel_on_connection_lost` are related.  
  
If I set `cancel_on_connection_lost = false`, Does Boost.Redis resend the request of step1 automatically ?  
  
Additional question: Is `cancel_if_unresponded` related to the situation?

---

## Comment 1

> Username: redboltz  
> Created at: 2024-01-25 07:25:45 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1909491426  

I hadn't known how to setup Case2 situation.   
Now, I found a way.  
  
1. added `redis.conf` to `enable-debug-command local` line.  
2. daemon-reload and restart redis service  
  
Then, redis server start accepting DEBUG commands.  
  
For example:  
  
```  
> redis-cli  
DEBUG SLEEP 10  
```  
  
10 seconds later I got  
  
```  
> redis-cli  
DEBUG SLEEP 10  
OK  
(10.00s)  
```  
  
This means we can test redis server delayed response.  
  
----  
  
I wrote the following test code using Boost.Redis 1.84.0.  
  
```cpp  
#include <string>  
#include <iostream>  
#include <optional>  
#include <chrono>  
  
#include <boost/asio.hpp>  
#include <boost/redis/src.hpp>  
  
namespace asio = boost::asio;  
namespace redis = boost::redis;  
  
int main() {  
    asio::io_context ioc;  
    redis::config cfg;  
    cfg.health_check_interval = std::chrono::seconds(0);  
  
    redis::request::config req_cfg;  
    req_cfg.cancel_on_connection_lost = false; // need to set false  
    req_cfg.cancel_if_not_connected = false;  
    req_cfg.cancel_if_unresponded = false;     // need to set false  
    req_cfg.hello_with_priority = true;  
  
    redis::request req{req_cfg};  
  
    redis::response<std::optional<std::string>> resp;  
  
    auto conn = std::make_shared<redis::connection>(ioc.get_executor());  
  
    conn->async_run(cfg, {}, asio::consign(asio::detached, conn));  
    req.push("DEBUG", "SLEEP", 10);  
    std::cout << "please restart redis" << std::endl;  
    conn->async_exec(  
        req,  
        resp,  
        [&](boost::system::error_code const& ec, std::size_t size) {  
            std::cout << "ec:" << ec.message() << std::endl;  
            std::cout << "size:" << size << std::endl;  
            std::cout << "resp.has_value():" << std::get<0>(resp).has_value() << std::endl;  
            if (std::get<0>(resp).has_value()) {  
                if (std::get<0>(resp).value()) {  
                    std::cout << "resp.value():" << *std::get<0>(resp).value() << std::endl;  
                }  
                else {  
                    std::cout << "nullopt" << std::endl;  
                }  
            }  
            std::cout << "resp.has_error():" << std::get<0>(resp).has_error() << std::endl;  
            if (std::get<0>(resp).has_error()) {  
                std::cout << "resp.error().diagnostic:" << std::get<0>(resp).error().diagnostic << std::endl;  
            }  
            conn->cancel();  
        }  
    );  
    ioc.run();  
}  
```  
  
```  
please restart redis  
ec:Success  
size:5  
resp.has_value():1  
resp.value():OK  
resp.has_error():0  
```  
  
When I see the output `please restart redis`, I restarted redis service. Even if redis is restarted, the `async_exec()` continues as I expected. (DEBUG SLEEP 10 request sent after reconnected).  
  
I tested the combination of `cancel_if_unresponded` and `cancel_on_connection_lost`.  
  
  
connection_lost \ unresponded|true|false  
---|---|---  
true|failed `ec:Operation canceled`|failed `ec:Operation canceled`  
false|failed `ec:Operation canceled`|success `ec:Success`   
  
When only I set both `cancel_if_unresponded` and `cancel_on_connection_lost` to `false` then I got expected result.  
  
I'm not sure why I need to set `cancel_if_unresponded` to false.  
Could you tell me why?

---

## Comment 2

> Username: criatura2  
> Created at: 2024-01-25 08:07:59 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1909603820  

> When only I set both cancel_if_unresponded and cancel_on_connection_lost to false then I got expected result.  
>   
> I'm not sure why I need to set cancel_if_unresponded to false.  
> Could you tell me why?  
  
- `cancel_if_unresponded` causes requests that have been written to the socket to fail. This is the important one in you case since the payload is written to the socket but the DEBUG command prevents Redis from responding to it.  
  
- `cancel_on_connection_lost` causes requests that haven't been written to the socket to fail. I think this is not your problem.

---

## Comment 3

> Username: redboltz  
> Created at: 2024-01-25 08:17:19 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1909630969  

@criatura2 , thank you for the comment. I understand `cancel_if_unresponded` is working in my case2.  
  
> - cancel_on_connection_lost causes requests that haven't been written to the socket to fail. I think this is not your problem.  
  
I set request config as follows for testing:  
  
```cpp  
    redis::request::config req_cfg;  
    req_cfg.cancel_on_connection_lost = true; // for testing set true  
    req_cfg.cancel_if_not_connected = false;  
    req_cfg.cancel_if_unresponded = false;     // need to set false  
    req_cfg.hello_with_priority = true;  
  
    redis::request req{req_cfg};  
```  
  
After `please restart redis` message output (I wait more 3 seconds), I restart redis service. Then `async_exec` returns immediately and I got the following error code.  
  
```  
please restart redis    ... restart redis 3 seconds later  
ec:Operation canceled  
size:0  
resp.has_value():1  
nullopt  
resp.has_error():0  
```  
  
Why does it happen?

---

## Comment 4

> Username: mzimbres  
> Created at: 2024-01-25 22:13:12 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1911084637  

Hi, can't reproduce the problem. Your test program has the correct behavior in my tests  
  
```  
please restart redis  
ec:Success  
size:5  
resp.has_value():1  
resp.value():OK  
resp.has_error():0  
```  
  
Can you please enable logging to better undestand what is happening, use debug level like this  
  
```cpp  
conn->async_run(cfg, {redis::logger::level::debug}, asio::consign(asio::detached, conn));  
```  
  
In my computer this results in  
  
```  
(Boost.Redis) check-health-op: timeout disabled.  
please restart redis  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 87 bytes written.  
=========> Here I restart redis <==========  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) reader-op: EOF received. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Success (reader), Success (writer)  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Success (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost.  
(Boost.Redis) check-health-op: timeout disabled.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 87 bytes written.  
(Boost.Redis) reader-op: 149 bytes read.  
(Boost.Redis) hello-op: Success  
ec:Success  
size:5  
resp.has_value():1  
resp.value():OK  
resp.has_error():0  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Success (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
```

---

## Comment 5

> Username: redboltz  
> Created at: 2024-01-25 23:59:15 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1911189871  

Here is my log with debug log enabled:  
  
```  
(Boost.Redis) check-health-op: timeout disabled.  
please restart redis  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 87 bytes written.  
  
=========> Here I restart redis <==========  
  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) reader-op: EOF received. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Success (reader), Success (writer)  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Success (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost.  
ec:Operation canceled  
size:0  
resp.has_value():1  
nullopt  
resp.has_error():0  
```  
  
Just in case, I paste whole code:  
  
```cpp  
#include <string>  
#include <iostream>  
#include <optional>  
#include <chrono>  
  
#include <boost/asio.hpp>  
#include <boost/redis/src.hpp>  
  
namespace asio = boost::asio;  
namespace redis = boost::redis;  
  
int main() {  
    asio::io_context ioc;  
    redis::config cfg;  
    cfg.health_check_interval = std::chrono::seconds(0);  
  
    redis::request::config req_cfg;  
    req_cfg.cancel_on_connection_lost = true; // for testing set true  
    req_cfg.cancel_if_not_connected = false;  
    req_cfg.cancel_if_unresponded = false;     // need to set false  
    req_cfg.hello_with_priority = true;  
  
    redis::request req{req_cfg};  
  
    redis::response<std::optional<std::string>> resp;  
  
    auto conn = std::make_shared<redis::connection>(ioc.get_executor());  
  
    conn->async_run(cfg, {redis::logger::level::debug}, asio::consign(asio::detached, conn));  
    req.push("DEBUG", "SLEEP", 10);  
    std::cout << "please restart redis" << std::endl;  
    conn->async_exec(  
        req,  
        resp,  
        [&](boost::system::error_code const& ec, std::size_t size) {  
            std::cout << "ec:" << ec.message() << std::endl;  
            std::cout << "size:" << size << std::endl;  
            std::cout << "resp.has_value():" << std::get<0>(resp).has_value() << std::endl;  
            if (std::get<0>(resp).has_value()) {  
                if (std::get<0>(resp).value()) {  
                    std::cout << "resp.value():" << *std::get<0>(resp).value() << std::endl;  
                }  
                else {  
                    std::cout << "nullopt" << std::endl;  
                }  
            }  
            std::cout << "resp.has_error():" << std::get<0>(resp).has_error() << std::endl;  
            if (std::get<0>(resp).has_error()) {  
                std::cout << "resp.error().diagnostic:" << std::get<0>(resp).error().diagnostic << std::endl;  
            }  
            conn->cancel();  
        }  
    );  
    ioc.run();  
}  
```

---

## Comment 6

> Username: mzimbres  
> Created at: 2024-01-26 22:19:57 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1912770655  

It is behaving as if reconnection had been disabled but without being able to reproduce it it is hard to tell. Does `async_run` return? For example, try replacing  
  
```cpp  
conn->async_run(cfg, {redis::logger::level::debug}, asio::consign(asio::detached, conn));  
```  
with  
  
```cpp  
conn->async_run(cfg, {redis::logger::level::debug}, [conn](auto ec){  
 std::cout << "async_run: " << ec.message() << std::endl;  
});  
```  
  
and let me know if you see the output

---

## Comment 7

> Username: redboltz  
> Created at: 2024-01-27 01:37:54 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1912903442  

I tested it. Here is the result:  
  
```  
(Boost.Redis) check-health-op: timeout disabled.  
please restart redis  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 87 bytes written.  
=========> Here I restart redis <==========  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) reader-op: EOF received. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Success (reader), Success (writer)  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Success (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost.  
ec:Operation canceled  
size:0  
resp.has_value():1  
nullopt  
resp.has_error():0  
async_run: Operation canceled  
```  
  
The last line, async_run seems to be returned.  
  
Perhaps it is caused by `conn->cancel()`. So I removed `conn->cancel()` for tesitng.  
  
Here is the result:  
  
```  
(Boost.Redis) check-health-op: timeout disabled.  
please restart redis  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 87 bytes written.  
=========> Here I restart redis <==========  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) reader-op: EOF received. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Success (reader), Success (writer)  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Success (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost.  
ec:Operation canceled  
size:0  
resp.has_value():1  
nullopt  
resp.has_error():0  
(Boost.Redis) check-health-op: timeout disabled.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 53 bytes written.  
(Boost.Redis) reader-op: 144 bytes read.  
(Boost.Redis) hello-op: Success  
```  
  
Program is not finished. async_run() callback is not invoked.

---

## Comment 8

> Username: mzimbres  
> Created at: 2024-01-27 12:30:32 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1913142852  

Still can't understand where is the problem. I will create a branch with improved logging so we can narrow down the problem. By the way, are you using the develop or master branch?

---

## Comment 9

> Username: redboltz  
> Created at: 2024-01-27 12:33:57 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1913143719  

I tested both Boost.Redis 1.84.0 and develop (commit hash 0445e74fa36d1460b0fad442bee3f2bddf6186e2), then got the same result.

---

## Comment 10

> Username: mzimbres  
> Created at: 2024-01-27 21:58:39 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1913346983  

I have found the problem: When Redis is restarted the read operation fails and calls `cancel(operation::run)` [here](https://github.com/boostorg/redis/blob/0445e74fa36d1460b0fad442bee3f2bddf6186e2/include/boost/redis/detail/connection_base.hpp#L319), which iterates over all requests and marks them as [waiting](https://github.com/boostorg/redis/blob/0445e74fa36d1460b0fad442bee3f2bddf6186e2/include/boost/redis/detail/connection_base.hpp#L543) if the appropriate flags are set (which is the case here) with the purpose of enabling resending the request once a new connection is stablished.  
  
Later on the internal hello command failed because it was coalesced with the `DEBUG` command and therefore was also waiting for completion while Redis was restarted. The implementation then [called](https://github.com/boostorg/redis/blob/0445e74fa36d1460b0fad442bee3f2bddf6186e2/include/boost/redis/detail/runner.hpp#L56) `cancel(operation::run)` again redundantly. At this time however the `DEBUG` command had already been marked as waiting by the previous `cancel` and enters the false branch [here](https://github.com/boostorg/redis/blob/0445e74fa36d1460b0fad442bee3f2bddf6186e2/include/boost/redis/detail/connection_base.hpp#L527), but since `cancel_on_connection_lost` is set to `true`, the request is cancelled.  
  
I am still unsure how to fix this: I can use _brute force_ by setting a flag `cancel_called` on the connection to avoid redundant calls or review all paths carefully to avoid more than one call to cancel.

---

## Comment 11

> Username: redboltz  
> Created at: 2024-01-28 12:21:45 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1913578204  

@mzimbres , thank you for the comment. It is good that you found the problem.  
When you would fix or do more analysis, If you would need more tests on my environment, please let me know. I will do that.  
  
By the way, Boost.Redis user's point of view, when I set both `cancel_if_unresponded` and `cancel_on_connection_lost` false, I got my expected behavior. (My expecting behavior is always retrying uncomplete request on reconnection. So far, I will do that. Is that working well?

---

## Comment 12

> Username: mzimbres  
> Created at: 2024-01-28 21:48:23 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1913732377  

> My expecting behavior is always retrying uncomplete request on reconnection.  
  
This should work well but I did not make this the default behaviour because there is a small window for double execution of a request  
  
1. Boost.Redis sends a request that has `cancel_if_unresponded = false`.  
2. Redis server receives the request, executes it, writes to disk and crashes before sending the response.  
3. Service manager restarts Redis   
4. Boost.Redis reconnects and the request is executed a second time.  
  
This is an unlikely theoretical possibility, so I decided to be conservative.  
  
> So far, I will do that. Is that working well?  
  
There are a couple of tests testing these flags, `cancel_on_connection_lost` is the hardest to test since Boost.Redis implements full-duplex communication and thefore it does not wait for a response in order to write the next request. This means, the time a request remains unwritten waiting on the queue is in the milliseconds.

---

## Comment 13

> Username: redboltz  
> Created at: 2024-01-29 01:01:28 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1913795620  

Thank you for the detail comment. It is very helpful to understand what is the difficult case.  
  
> > My expecting behavior is always retrying uncomplete request on reconnection.  
>   
> This should work well but I did not make this the default behaviour because there is a small window for double execution of a request  
>   
> 1. Boost.Redis sends a request that has `cancel_if_unresponded = false`.  
> 2. Redis server receives the request, executes it, writes to disk and crashes before sending the response.  
> 3. Service manager restarts Redis  
> 4. Boost.Redis reconnects and the request is executed a second time.  
>   
> This is an unlikely theoretical possibility, so I decided to be conservative.  
  
If I understand correctly, if users only use  idempotence request, it is not harmful. However, not all redis request is idempotence. For example `INCR`.   
  
By the way, in spite of Boost.Redis efforts, redis server could lost data by shutdown (e.g. power outage).  
I think that settings without data lose is only a combination of `AOF` (`appendonly yes`) with `appendfsync always`.  
See https://redis.io/docs/management/persistence/  
  
Fortunately, **my usecase** requires idempotence operations only. So I think that I can set `cancel_if_unresponded = false` on my code. (It doesn't mean it should be default. I can pass it as config argument).  
  
>   
> > So far, I will do that. Is that working well?  
>   
> There are a couple of tests testing these flags, `cancel_on_connection_lost` is the hardest to test since Boost.Redis implements full-duplex communication and thefore it does not wait for a response in order to write the next request. This means, the time a request remains unwritten waiting on the queue is in the milliseconds.  
  
I am not sure you already prepared the mechanisim but I guess that you need to prepare RESP3 mock server or timing controlable client side mock for testing. Then you can create any subtle timing in the test code.  
  
Or test-mode code could be required in the library side code (it is not an ideal).

---

## Comment 14

> Username: mzimbres  
> Created at: 2024-02-04 14:38:10 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1925777728  

I have fixed the problem in this branch https://github.com/boostorg/redis/tree/181-question-about-boostredisrequestconfig. Can you have a look and let me know if it solves the problem on your side? Thanks.

---

## Comment 15

> Username: redboltz  
> Created at: 2024-02-05 02:05:22 UTC  
> Updated at: 2024-02-05 03:26:46 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1926096040  

@mzimbres   
  
I tests your branch on my environment. It seems that the redis request finally sent and got expected result.  
Here is the output:  
  
```  
(Boost.Redis) check-health-op: timeout disabled.  
please restart redis  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 87 bytes written.  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) reader-op: EOF received. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Success (reader), Success (writer)  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Success (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost.  
(Boost.Redis) check-health-op: timeout disabled.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 87 bytes written.  
  
==== about 10 seconds wait ====  
  
(Boost.Redis) reader-op: 149 bytes read.  
(Boost.Redis) hello-op: Success  
ec:Success  
size:5  
resp.has_value():1  
resp.value():OK  
resp.has_error():0  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: Operation canceled (reader), Success (writer)  
(Boost.Redis) runner-op: Operation canceled (async_run_all), Success (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: Operation canceled  
async_run: Operation canceled  
```  
  
I added the comment `==== about 10 seconds wait ====`. It seems that Boost.Redis waits about 10 seconds for internal retrying. It quite long time for me. Is this intentional design? Or is there any way to configure the waiting time?  
  
NOTE:  
Boost.Redis 1.84.0 and set both `cancel_if_unresponded` and `cancel_on_connection_lost` to `false`, resending request and receiving expected result immediately.

---

## Comment 16

> Username: criatura2  
> Created at: 2024-02-05 07:58:55 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1926410912  

>I added the comment ==== about 10 seconds wait ====. It seems that Boost.Redis waits about 10 seconds for internal retrying. It quite long time for me. Is this intentional design? Or is there any way to configure the waiting time?  
  
The default retry interval is 1s. AFAICS, the 10s you are observing comes from `req.push("DEBUG", "SLEEP", 10);`. Are you restarting the server with `systemctl` as before?

---

## Comment 17

> Username: redboltz  
> Created at: 2024-02-05 08:10:08 UTC  
> Url: https://github.com/boostorg/redis/issues/181#issuecomment-1926425924  

@criatura2 , sorry it was my mistake.  
  
The 10 seconcs wait is form `req.push("DEBUG", "SLEEP", 10);`.  
This fix is fine for me :)  
Now, I can set  only  `cancel_if_unresponded` to false for my purpose.  
  
----   
  
> NOTE:  
> Boost.Redis 1.84.0 and set both cancel_if_unresponded and cancel_on_connection_lost to false, resending request and receiving expected result immediately.  
  
I wrote above comment. But I noticed that I used a wrong executable file.  
When I run again the correct file, then I got 10 seconds wait too. It is expected result.
