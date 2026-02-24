# #21 - example of reconnect [Closed]

> Username: wcy168  
> Created at: 2022-07-31 12:24:10 UTC  
> Updated at: 2022-08-23 07:00:19 UTC  
> Closed at: 2022-08-23 07:00:19 UTC  
> Url: https://github.com/boostorg/redis/issues/21  

Hi, can you provide an example of reconnection using synchronous mode?

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-31 12:38:28 UTC  
> Url: https://github.com/boostorg/redis/issues/21#issuecomment-1200417006  

I am working on it right now and will send feedback soon.

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-08-06 21:18:19 UTC  
> Url: https://github.com/boostorg/redis/issues/21#issuecomment-1207284130  

I have implemented automatic reconnection to the `connection` class in the master, for an example on how to use it see https://github.com/mzimbres/aedis/blob/c57f97b8c1d97ee5d23a6cac4c84fe0af1a19b36/examples/subscriber_sync.cpp#L58. Tomorrow I will release v0.3.0, for release notes see https://github.com/mzimbres/aedis/blob/c57f97b8c1d97ee5d23a6cac4c84fe0af1a19b36/CHANGELOG.md#v030.

---

## Comment 3

> Username: mzimbres  
> Created at: 2022-08-16 19:42:21 UTC  
> Url: https://github.com/boostorg/redis/issues/21#issuecomment-1217085788  

I found a bug on reconnecting in the context of events. This weekend I will release a bugfix release. Can we close this ticket?
