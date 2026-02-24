# #199 - async_exec never returns with server down [Closed]

> Username: strelez77  
> Created at: 2024-05-08 17:53:44 UTC  
> Updated at: 2024-07-28 08:58:52 UTC  
> Closed at: 2024-07-28 08:58:52 UTC  
> Url: https://github.com/boostorg/redis/issues/199  

Hi,  
I use ping command to check both the connection and the server health. The redis client health check is disabled in this case. In case the redis server goes down, the next happens:  
- first ping throws an exception.  will_reconnect returns true.  
- second ping (sent in a second) never returns from async_exec  
  
If I remember correctly, with Boost 1.84, I was able to ping infinitely, causing an exception. Once the server was back up and the connection was restored, the pings started returning true. My intention is to restart the server after several failures.  
It does not reproduce always and I have a workaround with timed operation, so it is not a critical for me. Here are the details in any case.  
  
My env:  
Windows 64  
Target build 32 bit  
boost version 1.85  
  
Here is the redis log in debug from the point the server goes down:  
(Boost.Redis) reader-op: (Boost.Redis) reader-op: An existing connection was forcibly closed by the remote host  
(Boost.Redis) reader-op: error. Exiting ...  
An existing connection was forcibly closed by the remote host(Boost.Redis) reader-op: An existing connection was forcibly closed by the remote host  
(Boost.Redis) reader-op: error. Exiting ...  
  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) reader-op: error. Exiting ...  
(Boost.Redis) run-op: (Boost.Redis) writer-op: canceled (3). Exiting ...An existing connection was forcibly closed by the remote host (reader),  
The operation completed successfully (writer)  
(Boost.Redis) runner-op: (Boost.Redis) An existing connection was forcibly closed by the remote host (async_run_all), run-op: The operation completed successfully (async_health_check) An existing connection was forcibly closed by the remote host (reader), The operation completed successfully (async_hello).The operation completed successfully (writer)  
  
(Boost.Redis) Connection lost: An existing connection was forcibly closed by the remote host  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: An existing connection was forcibly closed by the remote host (reader), The operation completed successfully (writer)  
(Boost.Redis) check-health-op: timeout disabled.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379, [::1]:6379  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: An existing connection was forcibly closed by the remote host (async_run_all), Pong timeout. (async_health_check) The operation completed successfully (async_hello).  
(Boost.Redis) Connection lost: An existing connection was forcibly closed by the remote host  
(Boost.Redis) check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: An existing connection was forcibly closed by the remote host (async_run_all), Pong timeout. (async_health_check) The operation completed successfully (async_hello).  
(Boost.Redis) Connection lost: An existing connection was forcibly closed by the remote host  
(Boost.Redis) run-all-op: resolve addresses (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379, 127.0.0.1:6379[::1]:6379  
, [::1]:6379  
(Boost.Redis) run-all-op: connected to endpoint No connection could be made because the target machine actively refused it  
  
(Boost.Redis) (Boost.Redis) check-timeout-op: Response has no value. Exiting ...check-timeout-op: Response has no value. Exiting ...  
(Boost.Redis)  
ping_op: error/cancelled (1).(Boost.Redis) ping_op: error/cancelled (1).  
  
(Boost.Redis) (Boost.Redis) check-health-op: check-health-op: The I/O operation has been aborted because of either a thread exit or an application request (async_ping), The I/O operation has been aborted because of either a thread exit or an application request (async_ping), Pong timeout. (async_check_timeout).Pong timeout. (async_check_timeout).  
  
(Boost.Redis) run-all-op: connected to endpoint (Boost.Redis) run-all-op: connected to endpoint No connection could be made because the target machine actively refused it  
No connection could be made because the target machine actively refused it

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-05-09 10:19:54 UTC  
> Url: https://github.com/boostorg/redis/issues/199#issuecomment-2102382737  

Do you have some code I could use to reproduce the problem?

---

## Comment 2

> Username: strelez77  
> Created at: 2024-05-09 13:59:26 UTC  
> Url: https://github.com/boostorg/redis/issues/199#issuecomment-2102719151  

I'll check if I can come up with something.

---

## Comment 3

> Username: VemundH  
> Created at: 2024-07-26 12:02:03 UTC  
> Url: https://github.com/boostorg/redis/issues/199#issuecomment-2252615012  

@strelez77 try to set `cancel_if_not_connected = true` in your ping requests
