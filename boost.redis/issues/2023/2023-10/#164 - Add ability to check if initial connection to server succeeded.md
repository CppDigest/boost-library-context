# #164 - Add ability to check if initial connection to server succeeded [Closed]

> Username: ferchor2003  
> Created at: 2023-10-23 22:33:03 UTC  
> Updated at: 2025-11-18 13:27:05 UTC  
> Closed at: 2025-11-18 13:27:05 UTC  
> Url: https://github.com/boostorg/redis/issues/164  

Some application will need to be able to check if initial connection to the server succeeded before sending any traffic to it.  
in other words, how can an app detect if the connection is up before trying to execute an exec() call?

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-11-03 20:09:05 UTC  
> Url: https://github.com/boostorg/redis/issues/164#issuecomment-1793033400  

The best way to check if a connection is up is by sending executing a command like `PING` with `exec()` command. In principle it is possible to check if the socket is open, but that is not as reliable. My recommendation however is for users to rely on the buil-in health-checker in Boost.Redis instead of trying to detect themselves. The health-checker is capable of establishing a new connection (to the same instance) when necessary.   
  
Also, I recommend you having a look at [redis::request::config](https://www.boost.org/doc/libs/develop/libs/redis/doc/html/classboost_1_1redis_1_1request.html#structboost_1_1redis_1_1request_1_1config) to adjust the behaviour to your needs, the flag `cancel_if_not_connected` seems to be what you are looking for.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-11-04 10:39:10 UTC  
> Url: https://github.com/boostorg/redis/issues/164#issuecomment-1793408107  

Above I mean the flag `boost::redis::request::config::cancel_if_not_connected`.  
  
Does that solve your problem? do your need any further help?

---

## Comment 3

> Username: mzimbres  
> Created at: 2025-11-18 13:27:05 UTC  
> Url: https://github.com/boostorg/redis/issues/164#issuecomment-3547654909  

> in other words, how can an app detect if the connection is up before trying to execute an exec() call?  
  
Boost.Redis supports cancellation now, you can use async_exec with timeout to check if the connection is up, see this example  
https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/example/cpp20_timeouts.cpp#L43
