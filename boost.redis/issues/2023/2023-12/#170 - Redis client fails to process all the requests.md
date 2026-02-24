# #170 - Redis client fails to process all the requests. [Closed]

> Username: tsar-rec  
> Created at: 2023-12-08 09:09:09 UTC  
> Updated at: 2024-01-22 10:25:14 UTC  
> Closed at: 2024-01-04 15:23:10 UTC  
> Url: https://github.com/boostorg/redis/issues/170  

We are migrating our system from Redis++ to Boost.Redis. We have a problem processing a relatively large number (~800) of HSET requests. About 80 of them are processed successfully, and after them the client seems to disconnect and all of them failed with 'Operation canceled' error.  
  
Any suggestions on how we can improve the implementation so that all our requests are processed without problems? The same use case works fine with Redis++.  
  
A simplified version of our code:  
```  
auto stream = "stream";  
for (const auto& [key, value]: map) {  
  auto req = make_shared<boost::redis::request>();  
  req.push("HSET", stream, key, payload(value));  
  _redis_client.async_exec(*req, boost::redis::ignore, [req](auto& ec, auto){ std::cerr<< (ec?"Failure":"Success") << '\n';}_  
}  
```  
  
If we try to push all our changes in the same request and send it to redis only once, it always fails.

---

## Comment 1

> Username: criatura2  
> Created at: 2023-12-08 09:13:21 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1846824789  

Can you please try to disable health-checks and try again? Long processing times might be causing the health-checks to fail.

---

## Comment 2

> Username: criatura2  
> Created at: 2023-12-08 09:50:13 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1846876437  

Here is how to disable: https://github.com/boostorg/redis/blob/723e72797fdfaecf0010fc6b3863b69f4073bdb1/include/boost/redis/config.hpp#L74

---

## Comment 3

> Username: steve-lorimer  
> Created at: 2023-12-08 10:34:20 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1846933349  

@criatura2 how do the health checks work please?   
  
If we are sending many requests successfully in a row surely that signals the connection is healthy?  
  
I'm trying to understand how having eg: 100 pending `async_exec` commands "in the queue" interferes with the health check?  
  
How do we know when to disable the health check and when to enable it? Is there a rule-of-thumb which says "if I've send X commands in the last Y seconds then the health check might incorrectly think we're dead"?

---

## Comment 4

> Username: tsar-rec  
> Created at: 2023-12-08 10:53:21 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1846956125  

Thank you for the quick reply! Turning off health checks works for the case with one huge request. If we are sending requests one by one, then about 600 of them are processed immediately well, and then the connection became unhealthy and process only 1 request per second.  
  
I don't understand why after the reconnect the connection is not capable to process more than one request.

---

## Comment 5

> Username: criatura2  
> Created at: 2023-12-08 11:15:54 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1846984841  

> @criatura2 how do the health checks work please?  
  
It is a loop that calls `async_exec` with the `PING` command every `n` seconds.  
  
> If we are sending many requests successfully in a row surely that signals the  
> connection is healthy?  
  
Yes.  
  
> I'm trying to understand how having eg: 100 pending async_exec commands "in  
> the queue" interferes with the health check?  
  
The health-checks are also queued for execution, so if the queue can't be  
processed fast enough it will timeout. It was an error to use `2s`, in future  
versions I will increase to `30s` or more.  
  
> How do we know when to disable the health check and when to enable it?  
  
I would always have it enabled. I recommend disabling it just to check if that  
was the problem.   
  
> Is there a rule-of-thumb which says "if I've send X commands in the last Y  
> seconds then the health check might incorrectly think we're dead"?  
  
If your client works under considerable load you might have to increase the  
timeout.  If you a using any commands that block the connection, such as  
`BLPOP` you will also want to increase or disable health-checks.  It depends on  
your needs.  
  
In the future the health checks will be improved to avoid most of these  
problems, see https://github.com/boostorg/redis/issues/104

---

## Comment 6

> Username: criatura2  
> Created at: 2023-12-08 11:24:51 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1846995811  

> Thank you for the quick reply! Turning off health checks works for the case  
> with one huge request. If we are sending requests one by one, then about 600 of  
> them are processed immediately well, and then the connection became unhealthy  
  
How did the connection became unhealthy if you disabled health-checks?  
  
> and process only 1 request per second.  
  
Is this after a reconnection? Are you making requests concurrently or one after  
the other? Can you send some code with with I can reproduce the problem?  
   
> I don't understand why after the reconnect the connection is not capable to  
> process more than one request.  
  
Ditto. I don't understand how it became unhealthy after disabling health-checks.  
  
Boost.Redis perform automatic pipelines so all concurrent requests will be  
assembled into one single payload and sent to the server.

---

## Comment 7

> Username: tsar-rec  
> Created at: 2023-12-08 11:37:30 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1847031419  

> > and process only 1 request per second.  
>   
> Is this after a reconnection? Are you making requests concurrently or one after the other?   
  
They are sent sequentially, in a loop like in the starting comment.  
  
> Can you send some code with with I can reproduce the problem?  
>   
 I'm preparing a benchmark that I can share here.

---

## Comment 8

> Username: tsar-rec  
> Created at: 2023-12-11 10:41:23 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1849787418  

The benchmark is now available at https://github.com/tsar-rec/boost-redis-benchmark. If compiled as described there, it can process 20 HSET requests of size 1000 each, but almost always fails (stuck) with 200 requests of size 1000 or larger request size.

---

## Comment 9

> Username: mzimbres  
> Created at: 2023-12-11 21:05:50 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1850887042  

Hi, thanks. I've had a look at your code and it seems to have some race conditions. I did not had the time to fix it but I have written an equivalent program using coroutine to check Boost.Redis has no problems, you can try this yourself  
  
```cpp  
#include <boost/redis/connection.hpp>  
#include <boost/asio/deferred.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/consign.hpp>  
#include <iostream>  
#include <iterator>  
  
#if defined(BOOST_ASIO_HAS_CO_AWAIT)  
  
namespace asio = boost::asio;  
using boost::redis::request;  
using boost::redis::ignore;  
using boost::redis::response;  
using boost::redis::config;  
using boost::redis::connection;  
  
// Called from the main function (see main.cpp)  
auto co_main(config cfg) -> asio::awaitable<void>  
{  
   std::string payload;  
   payload.resize(1024);  
   std::fill(std::begin(payload), std::end(payload), 'A');  
  
   int const repeat = 1000;  
  
   {  
      auto ex = co_await asio::this_coro::executor;  
      auto conn = std::make_shared<connection>(ex);  
  
      cfg.health_check_interval = std::chrono::seconds(0);  
      conn->async_run(cfg, {}, asio::consign(asio::detached, conn));  
  
      request req;  
      for (int i = 0; i < repeat; ++i)  
         req.push("HSET", "key1", std::to_string(i), payload);  
  
      co_await conn->async_exec(req, ignore, asio::deferred);  
      conn->cancel();  
   }  
  
   {  
      auto ex = co_await asio::this_coro::executor;  
      auto conn = std::make_shared<connection>(ex);  
  
      cfg.health_check_interval = std::chrono::seconds(0);  
      conn->async_run(cfg, {}, asio::consign(asio::detached, conn));  
  
      for (int i = 0; i < repeat; ++i) {  
         request req;  
         req.push("HSET", "key2", std::to_string(i), payload);  
         co_await conn->async_exec(req, ignore, asio::deferred);  
      }  
  
      conn->cancel();  
   }  
}  
  
#endif // defined(BOOST_ASIO_HAS_CO_AWAIT)  
```  
  
If you need help with Asio I recommend joining the #boost-beast channel on [cpplang](https://cppalliance.org/slack/).

---

## Comment 10

> Username: steve-lorimer  
> Created at: 2023-12-12 07:24:57 UTC  
> Updated at: 2023-12-12 07:31:10 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1851434288  

Thank you so much for helping dig into what is happening here.  
  
I'm not an expert in coroutines, so please correct me if I'm wrong, but doesn't this  
  
```  
co_await conn->async_exec(req, ignore, asio::deferred);  
```  
  
suspend the current caller until the send has completed, and only then continue?  
  
Perhaps it even suspends until the response has been received?  
  
If true, then this would effectively make the multiple sends happen one after the other, whereas with our non coroutine code, they're all being enqueued at once, and only after we return does control return to the asio `io_context` which then has an opportunity to send the commands

---

## Comment 11

> Username: steve-lorimer  
> Created at: 2023-12-12 07:27:44 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1851437572  

That is, the `co_await` suspends the calling coroutine and then resumes it when the `async_exec` is complete

---

## Comment 12

> Username: criatura2  
> Created at: 2023-12-12 08:04:36 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1851481872  

> Perhaps it even suspends until the response has been received?  
  
Correct.  
  
> If true, then this would effectively make the multiple sends happen one after  
> the other, whereas with our non coroutine code, they're all being enqueued at  
> once, and only after we return does control return to the asio io_context  
> which then has an opportunity to send the commands  
  
I did not bother testing this. So let me try it later today.

---

## Comment 13

> Username: mzimbres  
> Created at: 2023-12-12 20:48:15 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1852788749  

Indeed, I can reproduce the problem with this code  
  
```cpp  
      auto ex = co_await asio::this_coro::executor;  
      auto conn = std::make_shared<connection>(ex);  
  
      cfg.health_check_interval = std::chrono::seconds(0);  
      conn->async_run(cfg, {}, asio::consign(asio::detached, conn));  
  
      auto c = std::make_shared<int>(0);  
      for (int i = 0; i < repeat; ++i) {  
         auto req = std::make_shared<request>();  
         req->push("HSET", "key2", std::to_string(i), payload);  
         conn->async_exec(*req, ignore, [conn, req, c](auto ec, auto) {  
            if (ec) {  
               std::cout << ec.message() << std::endl;  
            } else {  
               std::cout << "--> " << *c << std::endl;  
               if (++*c == repeat) {  
                  std::cout << "done" << std::endl;  
                  conn->cancel();  
               }  
            }  
         });  
      }  
```  
I will investigate why this is causing trouble.

---

## Comment 14

> Username: mzimbres  
> Created at: 2023-12-14 22:31:43 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1856817449  

Found the problem and will fix in the code in the upcoming days.

---

## Comment 15

> Username: steve-lorimer  
> Created at: 2023-12-15 10:21:58 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1857624780  

That's great news, thank you!

---

## Comment 16

> Username: criatura2  
> Created at: 2023-12-19 08:04:30 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1862290745  

Hi, just a reminder the fix is now in the development branch, see above. If you test it, please let me know if it solves your issue. Thanks.

---

## Comment 17

> Username: steve-lorimer  
> Created at: 2023-12-19 08:07:55 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1862295205  

Brilliant, thank you - will feed back here once tested

---

## Comment 18

> Username: steve-lorimer  
> Created at: 2023-12-19 08:18:26 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1862308961  

Also just wanted to mention what a pleasure it is to read your code!

---

## Comment 19

> Username: tsar-rec  
> Created at: 2023-12-27 10:18:56 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1870159880  

Thank you for the update and sorry for the delay with the reply.  
  
Unfortunately the update does not completely fix the problem. The behaviour gets better for smaller number of requests, but with rather large setting (800 requests with 3000-byte payload) it fails pretty much every time. The failure looks the same: it stuck after sending 799 out of 800 requests and never sends the last one (or wrongly considers it already sent, but never execute a callback for it).  
  
I've updated a demo code a bit, to better match your example but keeping our use case (namely, sending a number of requests asynchronously without waiting for the result of individual ones).

---

## Comment 20

> Username: mzimbres  
> Created at: 2023-12-27 22:28:47 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1870666082  

Hi, thanks for the update. In the CI I am [testing](https://github.com/boostorg/redis/blob/2685d44ff2865404fd86f22d1722a5e82317aae0/test/test_conn_exec.cpp#L174) with 8k requests and 1kb payloads and it works fine, 10k/5kb as well.  I also have no problems with the number you are using i.e. 800/3kb.  
  
I will also have a look at your updated benchmarks to make sure I am doing exactly the same thing you are.  
  
Are you testing with a Redis server running in the localhost? The problem I fixed in the PR above was related to fact that Boost.Redis automatic pipelining uses a full-duplex communication, so under load and on a fast connection, such as in localhost, the answers start arriving before the write operation completed, this caused the connection thinking the message was a server push. You can easily check whether this is your problem by registering for server pushes with  
  
```cpp  
using boost::redis::resp3::to_string;  
using boost::redis::generic_response;  
  
generic_response resp;  
conn->set_receive_response(resp);  
  
conn->async_receive([conn](auto ec, auto n){  
   std::cout << "server push received" << std::endl;  
  
   for (auto const e: resp.value()) {  
         std::cout  
            << "type: " << to_string(e.type) << "\n"  
            << "size: " << to_string(e.aggregate_size) << "\n"  
            << "depth: " << to_string(e.depth) << "\n"  
            << "value: " << to_string(e.value) << "\n"  
            << std::endl;  
   }  
});  
```  
  
Let me know the output you receive, if any.  
  
Note to self:  
  
* Add an assert that ensures no non-push type is handled as a push.  
* Add an async_receive call in the test the PR above introduced, this will help understanding if anything goes wrong.

---

## Comment 21

> Username: mzimbres  
> Created at: 2023-12-30 13:03:38 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1872524332  

Hi again, I have forked your benchmark repo and found some problems. One issue is a race condition, another is the fact that you call `stop()` in the main function without waiting for the responses to arrive. After some simplifications I had not problem running the code.

---

## Comment 22

> Username: steve-lorimer  
> Created at: 2023-12-30 13:44:30 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1872530953  

Please could you give some details on the race condition?  
  
Perhaps you could do a PR back here so we can see what modifications you made?

---

## Comment 23

> Username: tsar-rec  
> Created at: 2024-01-03 10:48:09 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1875171962  

Hi Marcelo, can you please elaborate on the `stop()` issue? It is always called after the `run(bool, bool)` call, and the `run` function waits until all responses are received before exiting (see `waitForCompletion` call in the end of `run`). The intention was exactly to exit/stop only once all the responses have been received.  
  
Also I would be very grateful if you point out to the data race in the code.  
  
In the meantime I'm trying to modify your test case to match our use case.

---

## Comment 24

> Username: criatura2  
> Created at: 2024-01-03 15:19:25 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1875537966  

Hi, you have a race condition because the `io_context` is running  on a separate thread but you are interacting with the connection in the main thread, the connection however is not thead-safe. I don't know if this is the cause of your problem, but it should be fixed. I can try to fix the code later.

---

## Comment 25

> Username: mzimbres  
> Created at: 2024-01-03 16:13:38 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1875619477  

I have pushed some fixes in this fork https://github.com/mzimbres/boost-redis-benchmark and have no problem running it.

---

## Comment 26

> Username: tsar-rec  
> Created at: 2024-01-04 15:23:11 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1877278162  

Thank you for the feedback and code example. I've updated my example to factor out Redis and minimize API to communicate with it. I used `asio::post` similar to our internal use case. This version works very well, including very large values (I tested 8k requests with 300k payload each).  
  
I think the issue is solved now, so I'm closing it. Thank you once again for your help!

---

## Comment 27

> Username: tsar-rec  
> Created at: 2024-01-19 11:18:06 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1900220614  

Hello, do you have plans (and ETA) to merge the fix for this issue to `master` branch?

---

## Comment 28

> Username: mzimbres  
> Created at: 2024-01-21 20:59:50 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1902761873  

Hi, it is merged now. What does ETA stand for?

---

## Comment 29

> Username: tsar-rec  
> Created at: 2024-01-22 10:11:24 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1903660266  

Thanks a lot!   
  
ETA stands for 'Estimated Time of Arrival', in case it couldn't be merged immediately it would be useful to know when it can be done. But now it's irrelevant, as it is already in.

---

## Comment 30

> Username: tsar-rec  
> Created at: 2024-01-22 10:25:14 UTC  
> Url: https://github.com/boostorg/redis/issues/170#issuecomment-1903683181  

Now that the code is in master, what is the safest way to get the Boost sources that contain it? Is it to `git clone` Boost.org with submodules and then checkout the particular `boost.redis` commit? Or shall the whole `boost` be checkout to the 1.84.0 tag?
