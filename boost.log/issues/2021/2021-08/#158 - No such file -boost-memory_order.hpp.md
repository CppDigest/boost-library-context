# #158 - No such file  <boost/memory_order.hpp> [Closed]

> Username: IamtheMZI  
> Created at: 2021-08-11 21:36:36 UTC  
> Updated at: 2021-08-12 08:17:43 UTC  
> Closed at: 2021-08-12 08:17:43 UTC  
> Url: https://github.com/boostorg/log/issues/158  

https://github.com/boostorg/log/blob/d667377004fef39fe83932c0b15120a7b61a4cbb/include/boost/log/attributes/counter.hpp#L25  
  
Hi,  
 I am trying to build boost log with tag boost-1.75.0 and  I am getting an error like this  
  
`  
include/boost/log/attributes/counter.hpp:25:10: fatal error: boost/memory_order.hpp: No such file or directory  
   25 | #include <boost/memory_order.hpp>  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~  
`  
I tried to find this file but doesn't look like any such file exists.  
Can someone look into it or clarify this to me?  
Thanks.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-08-12 08:17:43 UTC  
> Url: https://github.com/boostorg/log/issues/158#issuecomment-897441112  

This file is part of [Boost.Atomic](https://github.com/boostorg/atomic/blob/a548b3c15c97d9c1b89e0bb16518baeaeb7a9aa3/include/boost/memory_order.hpp). Either your Boost download is incomplete or you haven't followed the building instructions.  
  
See [here](https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview#installing-boost) for building Boost from git. In particular, don't forget the `b2 headers` step.
