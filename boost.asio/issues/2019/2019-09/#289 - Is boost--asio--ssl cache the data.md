# #289 - Is boost::asio::ssl cache the data ? [Closed]

> Username: new-commer  
> Created at: 2019-09-24 13:04:25 UTC  
> Updated at: 2020-12-30 01:09:55 UTC  
> Closed at: 2020-12-30 01:09:54 UTC  
> Url: https://github.com/boostorg/asio/issues/289  

I'm using boost::asio::ssl  in my server , i have a problem .  
Sometimes it didn't send the data to peer(I checked it by tcpdump) , even though it succeed call boost::asio::async_write.  
In this case . Client will never receive the data no matter how long it wait . Unless it send another request to server. Then the data it expected arrived with a new data sent by server .  
Is boost cache the data ? How can i resolve it ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-01 15:13:07 UTC  
> Url: https://github.com/boostorg/asio/issues/289#issuecomment-548825455  

No, boost is not caching the data. Something is wrong with your server. Have you tried building and running the examples to verify that they work?

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:09:53 UTC  
> Url: https://github.com/boostorg/asio/issues/289#issuecomment-752292993  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#722](https://github.com/chriskohlhoff/asio/issues/722).
