# #195 - connection lost sporadically [Closed]

> Username: strelez77  
> Created at: 2024-03-27 18:56:15 UTC  
> Updated at: 2024-05-08 17:42:47 UTC  
> Closed at: 2024-05-08 17:42:47 UTC  
> Url: https://github.com/boostorg/redis/issues/195  

Hi,  
I experience a lot of disconnections in a simple test application. Where I run locally a redis server, connect to it and ping in debug in an infinite loop to check the behavior. Nothing complicate, but something does not seem to work properly.  
  
Windows system. Clion IDE with Visual Studio toolchain.  
I setup a connection with redis server using these commands  
  
mConnection = std::make_shared<connection>(co_await asio::this_coro::executor);  
mConnection->async_run(mConfig, redisLogger, asio::consign(asio::detached, mConnection));  
  
I used the default configuration.  
  
Sometimes it just executes fine over and over again, but eventually it sends disconnect packet to server and then the client reconnects.  
Redis server runs locally.  
  
Server log:  
[008748] 27 Mar 20:33:44.042 # RedisWin is starting ......  
[008748] 27 Mar 20:33:44.042 # RedisWin version=7.0.6, bits=64, commit=12/13/2022, modified=0, pid=8748, just started  
[008748] 27 Mar 20:33:44.042 # Configuration loaded  
[008748] 27 Mar 20:33:44.044 * monotonic clock: POSIX clock_gettime  
                _._  
           _.-``__ ''-._  
      _.-``    `.  `_.  ''-._           RedisWin 7.0.6 (12/13/2022/0) 64 bit  
  .-`` .-```.  ```\/    _.,_ ''-._  
 (    '      ,       .-`  | `,    )     Running in standalone mode  
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379  
 |    `-._   `._    /     _.-'    |     PID: 8748  
  `-._    `-._  `-./  _.-'    _.-'  
 |`-._`-._    `-.__.-'    _.-'_.-'|  
 |    `-._`-._        _.-'_.-'    |  
  `-._    `-._`-.__.-'_.-'    _.-'  
 |`-._`-._    `-.__.-'    _.-'_.-'|  
 |    `-._`-._        _.-'_.-'    |  
  `-._    `-._`-.__.-'_.-'    _.-'  
      `-._    `-.__.-'    _.-'  
          `-._        _.-'  
              `-.__.-'  
  
[008748] 27 Mar 20:33:44.048 # Server initialized  
[008748] 27 Mar 20:33:44.048 . The AOF directory appendonlydir doesn't exist  
[008748] 27 Mar 20:33:44.049 * Loading RDB produced by version 7.0.6  
[008748] 27 Mar 20:33:44.050 * RDB age 13 seconds  
[008748] 27 Mar 20:33:44.051 * RDB memory usage when created 0.44 Mb  
[008748] 27 Mar 20:33:44.051 * Done loading RDB, keys loaded: 0, keys expired: 0.  
[008748] 27 Mar 20:33:44.052 * DB loaded from disk: 0.003 seconds  
[008748] 27 Mar 20:33:44.052 * Ready to accept connections tcp  
[008748] 27 Mar 20:33:44.053 . 0 clients connected (0 replicas), 463832 bytes in use  
[008748] 27 Mar 20:33:49.536 . 0 clients connected (0 replicas), 463832 bytes in use  
[008748] 27 Mar 20:33:55.025 . 0 clients connected (0 replicas), 463832 bytes in use  
[008748] 27 Mar 20:34:00.543 . 0 clients connected (0 replicas), 463832 bytes in use  
[008748] 27 Mar 20:34:01.607 - Accepted 127.0.0.1:65108  
[008748] 27 Mar 20:34:06.050 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:11.576 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:17.091 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:22.625 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:28.153 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:33.672 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:39.180 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:39.280 - Reading from client: An existing connection was forcibly closed by the remote host.  
[008748] 27 Mar 20:34:44.690 . 0 clients connected (0 replicas), 463848 bytes in use  
[008748] 27 Mar 20:34:45.720 - Accepted 127.0.0.1:65144  
[008748] 27 Mar 20:34:50.203 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:55.719 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:34:57.087 - Reading from client: An existing connection was forcibly closed by the remote host.  
[008748] 27 Mar 20:35:01.240 . 0 clients connected (0 replicas), 463848 bytes in use  
[008748] 27 Mar 20:35:02.127 - Accepted 127.0.0.1:65168  
[008748] 27 Mar 20:35:06.764 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:35:12.265 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:17.772 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:23.292 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:28.804 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:34.317 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:39.828 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:45.354 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:50.882 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:35:56.403 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:36:01.880 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:36:07.365 . 1 clients connected (0 replicas), 466224 bytes in use  
[008748] 27 Mar 20:36:12.859 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:36:18.336 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:36:23.808 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:36:29.295 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:36:34.773 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:36:40.247 . 1 clients connected (0 replicas), 486696 bytes in use  
[008748] 27 Mar 20:36:44.074 - Reading from client: An existing connection was forcibly closed by the remote host.  
[008748] 27 Mar 20:36:45.717 . 0 clients connected (0 replicas), 463848 bytes in use  
[008748] 27 Mar 20:36:51.236 . 0 clients connected (0 replicas), 463848 bytes in use  
[008748] 27 Mar 20:36:56.755 . 0 clients connected (0 replicas), 463848 bytes in use  
[008748] 27 Mar 20:37:02.273 . 0 clients connected (0 replicas), 463848 bytes in use  
[008748] 27 Mar 20:37:07.790 . 0 clients connected (0 replicas), 463848 bytes in use  
  
Sometimes I see this error code in logger::on_connection_lost callback:  
{={={d1_={val_=1236, cat_=0x00d830c8 {test_boost_redis_client_integration.exe!boost::system::detail::system_error_category const boost::system::detail::system_cat_holder<void>::instance} {...}}, lc_flags_=12574221}, d2_=0x0118e2c8 "Ô\x4"}}  
  
Wireshark sniff is attached.  
  
Let me know if any additional information is needed.  
  
[sniff.zip](https://github.com/boostorg/redis/files/14780007/sniff.zip)

---

## Comment 1

> Username: strelez77  
> Created at: 2024-03-27 19:28:52 UTC  
> Updated at: 2024-03-27 19:29:15 UTC  
> Url: https://github.com/boostorg/redis/issues/195#issuecomment-2023804323  

I did the same test with another command (DBSIZE) and did not see this problem. It seems the health check ping is somehow running into an issue with my pings (just a guess). The severity of this issue is low, but it can point to something.

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-03-27 19:36:36 UTC  
> Url: https://github.com/boostorg/redis/issues/195#issuecomment-2023819571  

Hi,  
  
> but eventually it sends disconnect packet to server  
  
Can you elaborate?  
  
Also, please construct `redisLogger` with `boost::redis::logger::debug` and paste here the logging you will get, thanks.

---

## Comment 3

> Username: strelez77  
> Created at: 2024-04-30 07:58:30 UTC  
> Url: https://github.com/boostorg/redis/issues/195#issuecomment-2084639719  

I retested this scenario with even simpler flow. The client connects to redis and sits idle, except the health check with the ping. I see a reset after several seconds issued by the client. Here's the log as you requested:  
  
(Boost.Redis) writer-op: 14 bytes written.  
(Boost.Redis) reader-op: 7 bytes read.  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) reader-op: The network connection was aborted by the local system  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: The network connection was aborted by the local system (reader), The operation completed successfully (writer)  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: The network connection was aborted by the local system (async_run_all), Pong timeout. (async_health_check) The operation completed successfully (async_hello).  
(Boost.Redis) Connection lost: The network connection was aborted by the local system  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379, [::1]:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 85 bytes written.  
(Boost.Redis) reader-op: 162 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
[sniff.zip](https://github.com/boostorg/redis/files/15160150/sniff.zip)

---

## Comment 4

> Username: mzimbres  
> Created at: 2024-05-01 14:49:50 UTC  
> Url: https://github.com/boostorg/redis/issues/195#issuecomment-2088577424  

Hi, I think the important line in the log is  
  
> (Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
  
I am trying to reproduce it and will get back to you.

---

## Comment 5

> Username: mzimbres  
> Created at: 2024-05-04 15:33:10 UTC  
> Url: https://github.com/boostorg/redis/issues/195#issuecomment-2094260180  

I left a client running for many hours and hadn't any problem. Can you provide more details about how you are using Redis so I can try to reproduce?

---

## Comment 6

> Username: strelez77  
> Created at: 2024-05-08 17:42:47 UTC  
> Url: https://github.com/boostorg/redis/issues/195#issuecomment-2101088314  

It is resolved with boost 1.85 version. I consider this issue resolved.
