# #18 - fail to reconnect [Closed]

> Username: wcy168  
> Created at: 2022-07-26 08:29:21 UTC  
> Updated at: 2022-07-30 00:03:45 UTC  
> Closed at: 2022-07-30 00:03:45 UTC  
> Url: https://github.com/boostorg/redis/issues/18  

When I run the subscriber example, stopping the redis server for a while and restarting the redis server will cause the program to crash

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-26 16:46:03 UTC  
> Url: https://github.com/boostorg/redis/issues/18#issuecomment-1195726575  

I can reproduce the problem, will fix and give feedback soon. Thanks for reporting.

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-07-26 20:12:07 UTC  
> Url: https://github.com/boostorg/redis/issues/18#issuecomment-1195931379  

Fixed on the master. I will add some more tests and make a bugfix release on the weekend.

---

## Comment 3

> Username: Eddie-cz  
> Created at: 2022-07-27 10:21:15 UTC  
> Url: https://github.com/boostorg/redis/issues/18#issuecomment-1196544091  

Just small issue in that example, subscribe is to 'channel', comment points to channel1 (PUBLISH channel1 some-message)
