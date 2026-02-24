# #2355 - does wss support cdn forwarding [Closed]

> Username: romanholidaypancakes  
> Created at: 2021-12-08 09:37:36 UTC  
> Updated at: 2021-12-09 03:17:09 UTC  
> Closed at: 2021-12-09 03:17:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2355  

The wss client of another library I am using cannot be accessed once cdn(cloudflare) is turned on. I want to know whether this library supports cdn

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-12-08 10:46:19 UTC  
> Updated at: 2021-12-08 10:47:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2355#issuecomment-988700907  

Cloudflare requires passing a couple of headers I think? Beast can certainly do that.  
  
Can you detail cloudflare's requirements here?  
  
https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream_base__decorator.html

---

## Comment 2

> Username: romanholidaypancakes  
> Created at: 2021-12-08 11:24:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2355#issuecomment-988728413  

I see that grpc has request header requirements, but the weboscket requirements are not found. I access the cloudflare-protected website via browser f12 and I don’t find anything special. I need to continue to search for information.  
  
https://support.cloudflare.com/hc/en-us/articles/360050483011-Understanding-Cloudflare-gRPC-support  
  
https://support.cloudflare.com/hc/en-us/articles/200169466-Using-Cloudflare-with-WebSockets

---

## Comment 3

> Username: romanholidaypancakes  
> Created at: 2021-12-09 03:17:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2355#issuecomment-989470225  

give up the use of cdn forwarding to solve this problem.
