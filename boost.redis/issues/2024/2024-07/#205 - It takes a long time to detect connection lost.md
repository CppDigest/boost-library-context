# #205 - It takes a long time to detect connection lost [Closed]

> Username: VemundH  
> Created at: 2024-07-26 11:58:47 UTC  
> Updated at: 2024-09-15 09:04:49 UTC  
> Closed at: 2024-09-15 09:04:49 UTC  
> Url: https://github.com/boostorg/redis/issues/205  

Small test program based on the examples in this repo:  
  
```c++  
#include <boost/redis/connection.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/redirect_error.hpp>  
#include <boost/asio/steady_timer.hpp>  
  
#include <iostream>  
#include <chrono>  
  
namespace asio = boost::asio;  
using boost::redis::request;  
using boost::redis::ignore;  
using boost::redis::config;  
using boost::redis::connection;  
using boost::redis::logger;  
using boost::asio::awaitable;  
using boost::asio::detached;  
using boost::asio::consign;  
  
awaitable<void> run_publisher(std::shared_ptr<connection> conn)  
{  
  asio::steady_timer timer(co_await asio::this_coro::executor);  
  while (conn->will_reconnect())  
  {  
    request req;  
    req.push("PUBLISH", "channel", "some data");  
    boost::system::error_code ec;  
    co_await conn->async_exec(req, ignore, asio::redirect_error(asio::use_awaitable, ec));  
    if (ec)  
    {  
      std::clog << std::chrono::system_clock::now()  
        << " PUBLISH failed with message: " << ec.message()  
        << std::endl;  
    }  
    timer.expires_after(std::chrono::seconds(2));  
    co_await timer.async_wait(asio::redirect_error(asio::use_awaitable, ec));  
  }  
}  
  
awaitable<void> co_main(config cfg)  
{  
  cfg.health_check_interval = std::chrono::seconds(30);  
  auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
  conn->async_run(cfg, {logger::level::debug}, consign(detached, conn));  
  
  co_await run_publisher(conn);  
  conn->cancel();  
}  
```  
  
Using 30 seconds health check interval as indicated in this [comment](https://github.com/boostorg/redis/issues/170#issuecomment-1846984841) will be default in a future version.  
  
Typically seeing a delay between 3x to 4x health check interval after restarting Redis before connection lost is detected and reconnect attempted.  
  
When restarting Redis the run-op completes almost immediately. This log shows that almost 2 min later connection lost is detected:  
  
```  
2024-07-26 10:54:51.1038269 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-07-26 10:54:51.1085491 (Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
2024-07-26 10:54:51.1090630 (Boost.Redis) writer-op: 130 bytes written.  
2024-07-26 10:54:51.1110903 (Boost.Redis) reader-op: 973 bytes read.  
2024-07-26 10:54:51.1117493 (Boost.Redis) hello-op: Success  
2024-07-26 10:54:53.1252447 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:54:53.1268255 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:54:55.1416191 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:54:55.1428586 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:54:57.1517312 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:54:57.1527005 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:54:59.1575930 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:54:59.1581645 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:55:01.1650597 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:55:01.1659037 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:55:03.1787981 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:55:03.1792210 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:55:05.1378655 (Boost.Redis) reader-op: End of file  
2024-07-26 10:55:05.1382268 (Boost.Redis) reader-op: EOF received. Exiting ...  
2024-07-26 10:55:05.1386657 (Boost.Redis) writer-op: canceled (3). Exiting ...  
2024-07-26 10:55:05.1389448 (Boost.Redis) run-op: The operation completed successfully (reader), The operation completed successfully (writer)  
2024-07-26 10:56:51.1219054 (Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
2024-07-26 10:56:51.1228789 (Boost.Redis) ping_op: error/cancelled (1).  
2024-07-26 10:56:51.1236270 (Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).  
2024-07-26 10:56:51.1239811 (Boost.Redis) runner-op: The operation completed successfully (async_run_all), Pong timeout. (async_health_check) The operation completed successfully (async_hello).  
2024-07-26 10:56:51.1243785 (Boost.Redis) Connection lost.  
2024-07-26 10:56:52.1326278 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-07-26 10:56:52.1333834 (Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
2024-07-26 10:56:52.1337355 (Boost.Redis) writer-op: 130 bytes written.  
2024-07-26 10:56:52.1354276 (Boost.Redis) reader-op: 973 bytes read.  
2024-07-26 10:56:52.1358929 (Boost.Redis) hello-op: Success  
2024-07-26 10:56:54.1395472 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:56:54.1408228 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:56:56.1418841 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 10:56:56.1428140 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 10:56:58.1477065 (Boost.Redis) writer-op: 45 bytes written.  
```  
  
Added a small change to logger to get the timestamps:  
  
```c++  
void logger::write_prefix()  
{  
   std::clog << std::chrono::system_clock::now() << " ";  
   if (!std::empty(prefix_))  
      std::clog << prefix_;  
}  
```  
  
Setting `cancel_if_not_connected = true` in the request will result in `async_exec` failing with the expected not connected error:  
  
```  
2024-07-26 11:17:43.6053426 PUBLISH failed with message: Not connected.  
2024-07-26 11:17:43.6057837 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-07-26 11:17:43.6074385 (Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
2024-07-26 11:17:43.6078098 (Boost.Redis) writer-op: 85 bytes written.  
2024-07-26 11:17:43.6107975 (Boost.Redis) reader-op: 969 bytes read.  
2024-07-26 11:17:43.6115954 (Boost.Redis) hello-op: Success  
2024-07-26 11:17:45.6173984 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 11:17:45.6179272 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 11:17:47.6251202 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 11:17:47.6257825 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 11:17:49.6389446 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 11:17:49.6398523 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 11:17:49.9818766 (Boost.Redis) reader-op: End of file  
2024-07-26 11:17:49.9822220 (Boost.Redis) reader-op: EOF received. Exiting ...  
2024-07-26 11:17:49.9825654 (Boost.Redis) writer-op: canceled (3). Exiting ...  
2024-07-26 11:17:49.9827326 (Boost.Redis) run-op: The operation completed successfully (reader), The operation completed successfully (writer)  
2024-07-26 11:17:51.6439209 PUBLISH failed with message: Not connected.  
2024-07-26 11:17:53.6537792 PUBLISH failed with message: Not connected.  
2024-07-26 11:17:55.6638643 PUBLISH failed with message: Not connected.  
2024-07-26 11:17:57.6701620 PUBLISH failed with message: Not connected.  
2024-07-26 11:17:59.6732612 PUBLISH failed with message: Not connected.  
...  
2024-07-26 11:19:42.0683707 PUBLISH failed with message: Not connected.  
2024-07-26 11:19:43.6180784 (Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
2024-07-26 11:19:43.6187335 (Boost.Redis) ping_op: error/cancelled (1).  
2024-07-26 11:19:43.6193823 (Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).  
2024-07-26 11:19:43.6198053 (Boost.Redis) runner-op: The operation completed successfully (async_run_all), Pong timeout. (async_health_check) The operation completed successfully (async_hello).  
2024-07-26 11:19:43.6200351 (Boost.Redis) Connection lost.  
2024-07-26 11:19:44.0833046 PUBLISH failed with message: Not connected.  
2024-07-26 11:19:44.6271292 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-07-26 11:19:44.6283678 (Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
2024-07-26 11:19:44.6290895 (Boost.Redis) writer-op: 85 bytes written.  
2024-07-26 11:19:44.6397801 (Boost.Redis) reader-op: 969 bytes read.  
2024-07-26 11:19:44.6403232 (Boost.Redis) hello-op: Success  
2024-07-26 11:19:46.0852712 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 11:19:46.0861008 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 11:19:48.1011338 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 11:19:48.1018705 (Boost.Redis) reader-op: 4 bytes read.  
2024-07-26 11:19:50.1027101 (Boost.Redis) writer-op: 45 bytes written.  
2024-07-26 11:19:50.1036486 (Boost.Redis) reader-op: 4 bytes read.  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-08-03 14:17:31 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2266725969  

Thanks for the detailed analysis (and sorry for the delayed response).  
  
> Using 30 seconds health check interval as indicated in this comment  
> will be default in a future version.  
  
I am not yet sure what is better.  
  
> When restarting Redis the run-op completes almost immediately. This  
> log shows that almost 2 min later connection lost is detected:  
  
Yeah it takes 1min52s more precisely. My analysis below shows the following  
  
```ini  
# This is the log of the last successful health check. After it  
# completes a new ping is scheduled for 30s later or at  
# 10:55:03 + 30s = 10:55:33.  
# https://github.com/boostorg/redis/blob/e8dd4d69eb3524c9a0efb0c9723790b479a4ba8f/include/boost/redis/detail/health_checker.hpp#L56  
10:55:03 writer-op: 45 bytes written.  
10:55:03 reader-op: 4 bytes read.  
  
# This block is the first reaction to restarting the Redis server. The  
# server closed the connection cleanly by sending and EOF. Once the  
# read operation detects it and exits, the write op is also canceled  
# since I use wait_for_one in https://github.com/boostorg/redis/blob/e8dd4d69eb3524c9a0efb0c9723790b479a4ba8f/include/boost/redis/detail/connection_base.hpp#L196  
10:55:05 reader-op: End of file  
10:55:05 reader-op: EOF received. Exiting ...  
10:55:05 writer-op: canceled (3). Exiting ...  
10:55:05 run-op: The operation completed successfully (reader), The operation completed successfully (writer)  
  
# 30s passes since the last successfull health-check and the next one  
# is scheduled. But since there is no connection it sits there waiting  
# for a new connection to be stablished. If cancel_if_not_connected  
# were set in the health check it would fail immediately here, but  
# that can't be used reliably since it uses socket.is_open().  
10:55:33 ... # No log entry.  
  
# The check-health operation wakes up here to check if the  
# health-check pings are being received, cleans up the response of the  
# last successful health-check, that happended at 10:55:03 and goes  
# sleep for another 2 * 30s = 60s.  
# https://github.com/boostorg/redis/blob/e8dd4d69eb3524c9a0efb0c9723790b479a4ba8f/include/boost/redis/detail/health_checker.hpp#L81  
10:55:51 ... # No log entry.  
  
# After 60s the check-health operation wakes up just to see no  
# successful response was received, then it exits to the reconnect  
# loop to stablish a new connection.  
10:56:51 check-timeout-op: Response has no value. Exiting ...  
10:56:51 ping_op: error/cancelled (1).  
10:56:51 check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).  
10:56:51 runner-op: The operation completed successfully (async_run_all), Pong timeout. (async_health_check) The operation completed successfully (async_hello).  
10:56:51 Connection lost.  
10:56:52 run-all-op: resolve addresses 127.0.0.1:6379  
10:56:52 run-all-op: connected to endpoint 127.0.0.1:6379  
10:56:52 writer-op: 130 bytes written.  
10:56:52 reader-op: 973 bytes read.  
10:56:52 hello-op: Success  
```  
  
I am not sure yet, but the only place where I still can adjust to  
reduce the delay is on how long the check-health operation waits, at  
the moment it is `2 * ping_interval`, in principle it could be reduced  
to ping_interval + small number so that instead of 2min you would  
experience 1min30s.

---

## Comment 2

> Username: VemundH  
> Created at: 2024-08-05 08:20:08 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2268459909  

The design of the health checks looks OK.  
I think the actual problem is the `wait_for_all` here: https://github.com/boostorg/redis/blob/e8dd4d69eb3524c9a0efb0c9723790b479a4ba8f/include/boost/redis/detail/runner.hpp#L96   
When `async_run_all` completes the health check can no longer succeed and should be canceled.

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-08-17 19:21:11 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2294949981  

> I think the actual problem is the wait_for_all here:  
>  asio::experimental::wait_for_all(),   
> When async_run_all completes the health check can no longer succeed and should be canceled.  
  
Good point, perhaps wait_for_one_error is the correct token here, I will check.  
  
```cpp  
         BOOST_ASIO_CORO_YIELD  
         asio::experimental::make_parallel_group(  
            [this](auto token) { return runner_->async_run_all(*conn_, logger_, token); },  
            [this](auto token) { return runner_->health_checker_.async_check_health(*conn_, logger_, token); },  
            [this](auto token) { return runner_->async_hello(*conn_, logger_, token); }  
         ).async_wait(  
            asio::experimental::wait_for_all(),  
            std::move(self));  
```

---

## Comment 4

> Username: VemundH  
> Created at: 2024-08-19 08:15:35 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2295947216  

Testing with wait_for_one_error did not make any difference. Maybe because the sever disconnect is not considered an error?  
  
```  
2024-08-19 07:40:47.8739521 (Boost.Redis) writer-op: 45 bytes written.  
2024-08-19 07:40:47.8746217 (Boost.Redis) reader-op: 4 bytes read.  
2024-08-19 07:40:49.3554511 (Boost.Redis) reader-op: End of file  
2024-08-19 07:40:49.3557639 (Boost.Redis) reader-op: EOF received. Exiting ...  
2024-08-19 07:40:49.3560638 (Boost.Redis) writer-op: canceled (3). Exiting ...  
2024-08-19 07:40:49.3562413 (Boost.Redis) run-op: The operation completed successfully (reader), The operation completed successfully (writer)  
2024-08-19 07:42:31.8046087 (Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
2024-08-19 07:42:31.8054157 (Boost.Redis) ping_op: error/cancelled (1).  
2024-08-19 07:42:31.8061572 (Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).  
2024-08-19 07:42:31.8066630 (Boost.Redis) runner-op: The operation completed successfully (async_run_all), Pong timeout. (async_health_check) The operation completed successfully (async_hello).  
2024-08-19 07:42:31.8073355 (Boost.Redis) Connection lost.  
```

---

## Comment 5

> Username: VemundH  
> Created at: 2024-08-19 08:44:54 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2296011763  

As a simple test I added `conn_->cancel(operation::health_check);` here:  
https://github.com/boostorg/redis/blob/e8dd4d69eb3524c9a0efb0c9723790b479a4ba8f/include/boost/redis/detail/runner.hpp#L158  
  
Then I get:  
```  
2024-08-19 08:19:00.5407450 (Boost.Redis) writer-op: 45 bytes written.  
2024-08-19 08:19:00.5420529 (Boost.Redis) reader-op: 4 bytes read.  
2024-08-19 08:19:01.5827613 (Boost.Redis) reader-op: End of file  
2024-08-19 08:19:01.5830703 (Boost.Redis) reader-op: EOF received. Exiting ...  
2024-08-19 08:19:01.5833432 (Boost.Redis) writer-op: canceled (3). Exiting ...  
2024-08-19 08:19:01.5834968 (Boost.Redis) run-op: The operation completed successfully (reader), The operation completed successfully (writer)  
2024-08-19 08:19:01.5839292 (Boost.Redis) ping_op: error/cancelled (2).  
2024-08-19 08:19:01.5840782 (Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
2024-08-19 08:19:01.5842141 (Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), The I/O operation has been aborted because of either a thread exit or an application request (async_check_timeout).  
2024-08-19 08:19:01.5844371 (Boost.Redis) runner-op: The operation completed successfully (async_run_all), The I/O operation has been aborted because of either a thread exit or an application request (async_health_check) The operation completed successfully (async_hello).  
2024-08-19 08:19:01.5846814 (Boost.Redis) Connection lost.  
```  
  
The reasoning is that no more reads and writes will be attempted after `run_all_op` completes. Health check can no longer succeed. Canceling it instead of waiting for timeout will remove the long delay before reconnect logic is started.  
I have not familiarized myself enough with the code and design to see if this change will have any negative side-effects.

---

## Comment 6

> Username: anarthal  
> Created at: 2024-09-01 13:59:04 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2323351838  

Been out for a few days. Without being a Boost.Redis expert, I'd say @mzimbres diagnostics is mainly correct. But bear in mind that from this log line:  
  
```  
2024-07-26 10:55:05.1389448 (Boost.Redis) run-op: The operation completed successfully (reader), The operation completed successfully (writer)  
```  
  
[This op](https://github.com/boostorg/redis/blob/abde1afcb0d883bb77834a41aa2c54a26cd663e1/include/boost/redis/detail/runner.hpp#L92) is completing successfully. I don't know under which circumstances the other ops may complete, but I'd say it makes sense to cancel the other operations once one of them exits.  
  
Instead of `conn_->cancel(operation::health_check);`, I think using `asio::experimental::wait_for_one()` is better, as it provides exactly this semantics.  
  
The degree of nesting async ops makes testing quite difficult, so I'd suggest exploring the option of replacing some of these by a sans-io state machine, which usually leads to more testable code.

---

## Comment 7

> Username: mzimbres  
> Created at: 2024-09-08 20:27:58 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2336814860  

Back form summer vacation ...  
  
@VemundH I couldn't reproduce this problem so far, I tried to run Redis manually, on systemd and on docker as you explained in the PR. But I think you are  correct on the fact that since EOF is not considered and error the parallel group linked above will have to wait until the health-check returns an error. I have changed this in the PR [here](https://github.com/boostorg/redis/pull/208/files#diff-8076681d53ac42736477d12a1f62b53cb02879e46925592cd51aadc1625d5366R320). Can you please try again?

---

## Comment 8

> Username: VemundH  
> Created at: 2024-09-09 07:55:52 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2337391763  

@mzimbres I have tested with the changes in the PR and now the disconnect is detected when EOF is received. Thanks!  
  
```  
2024-09-09 07:39:57.9455664 (Boost.Redis) writer-op: 45 bytes written.  
2024-09-09 07:39:57.9461189 (Boost.Redis) reader-op: 4 bytes read.  
2024-09-09 07:39:58.8449635 (Boost.Redis) reader-op: End of file  
2024-09-09 07:39:58.8454481 (Boost.Redis) reader-op: EOF received. Exiting ...  
2024-09-09 07:39:58.8458182 (Boost.Redis) writer-op: canceled (3). Exiting ...  
2024-09-09 07:39:58.8460415 (Boost.Redis) run-op: The operation completed successfully (reader), The operation completed successfully (writer)  
2024-09-09 07:39:58.8465081 (Boost.Redis) ping_op: error/cancelled (2).  
2024-09-09 07:39:58.8467220 (Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
2024-09-09 07:39:58.8469090 (Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), The I/O operation has been aborted because of either a thread exit or an application request (async_check_timeout).  
2024-09-09 07:39:58.8472674 (Boost.Redis) runner-op: The operation completed successfully (async_run_all), The I/O operation has been aborted because of either a thread exit or an application request (async_health_check) The operation completed successfully (async_hello).  
2024-09-09 07:39:58.8476614 (Boost.Redis) Connection lost.  
```

---

## Comment 9

> Username: mzimbres  
> Created at: 2024-09-09 20:39:51 UTC  
> Url: https://github.com/boostorg/redis/issues/205#issuecomment-2339046185  

> This op is completing successfully. I don't know under which  
> circumstances the other ops may complete, but I'd say it makes sense  
> to cancel the other operations once one of them exits.  
  
Correct, that is achieved with the PR by making EOF an error and using `wait_for_one_error` instead of `wait_for_all`.  
  
> Instead of conn_->cancel(operation::health_check);, I think using  
> asio::experimental::wait_for_one() is better, as it provides exactly  
> this semantics.  
  
The problem with that set of operation is that two of them are long running (`async_run` and the health check) and the other is short lived (resp3 handshake) so if we used `wait_for_one` instead when the latter finishes it would trigger cancellation of the other two.  
  
> The degree of nesting async ops makes testing quite difficult, so  
> I'd suggest exploring the option of replacing some of these by a  
> sans-io state machine, which usually leads to more testable code.  
  
At first I tried to launch all operations in the same group i.e. read, write, handshake, health_check. That reduces the nested ops but bloated the connection class, that is why I split it in two classes. I agree however there is still room for improvements.
