# #104 - Make the health checker more flexible [Closed]

> Username: mzimbres  
> Created at: 2023-05-20 20:10:28 UTC  
> Updated at: 2025-10-20 13:29:22 UTC  
> Closed at: 2025-10-20 13:29:22 UTC  
> Url: https://github.com/boostorg/redis/issues/104  

At the moment if the response to a health-check does not come under the time specified by the user, the connection will timeout. This is not flexible enough to deal with e.g. heavy load where the specified interval cannot be honored because e.g. it is processing too many pushes.  
  
We can offer two options to users  
  
1. Inflexible checks: The connection times out if the specified interval is not honored. (Current implementation)  
2. Flexibel checks: Any incoming data, like server pushes are seen as a proof of health (and not only the response to a health-check).  
  
Option 2. covers my own needs, we might not need option 1.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-09-08 16:49:58 UTC  
> Url: https://github.com/boostorg/redis/issues/104#issuecomment-3267142032  

I'm reading this issue now and I'm convinced that 2 is the way to go. I believe we could integrate health check in the reader, writer and multiplexer completely, without any need of an extra layer. If I have the time, I'll write a PR.
