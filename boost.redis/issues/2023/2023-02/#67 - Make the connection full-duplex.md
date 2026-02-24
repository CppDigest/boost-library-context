# #67 - Make the connection full-duplex [Closed]

> Username: mzimbres  
> Created at: 2023-02-12 13:37:13 UTC  
> Updated at: 2023-02-18 20:39:14 UTC  
> Closed at: 2023-02-18 20:39:14 UTC  
> Url: https://github.com/boostorg/redis/issues/67  

Currently, write operations happens only after the response to previous command arrive. There is however nothing in the protocol that prohibits writing continuously as requests come.  
  
This is not likely to produced considerable (or even measurable) performance improvement due to the support for automatic pipelining, but there is also no reason not to implement it.
